<template>
  <div class="policy">
    <el-row type="flex" justify="center">
      <el-col :xs="24" :sm="24" :md="24" :lg="22" :xl="18">
        <div class="title">Add Folder Policies</div>
        <el-form class="add-policy-form" :model="policy" @submit.native.prevent="addPolicy" :rules="rules" ref="add-policy-form" inline>
          <el-form-item prop="subject">
            <el-select v-model="policy.subject" placeholder="Select user" filterable no-data-text="No data" no-match-text="No matching option">
              <el-option v-for="item in users" :key="item.value" :label="item.email" :value="item.user_id">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item prop="object">
            <el-select v-model="policy.object" placeholder="Select folder" filterable no-data-text="No data" no-match-text="No matching option">
              <el-option v-for="item in folders" :key="item.value" :label="item.name" :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item prop="action">
            <el-select v-model="policy.action" placeholder="Select action" filterable no-data-text="No data" no-match-text="No matching option">
              <el-option v-for="item in folderActions" :key="item" :label="item" :value="item">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item prop="effect">
            <el-select v-model="policy.effect" placeholder="Select effect" filterable no-data-text="No data" no-match-text="No matching option">
              <el-option v-for="item in effect" :key="item" :label="item" :value="item">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button :loading="loading" type="primary" @click="addPolicy">Add Policy</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import {
  listUsers
} from '@/api/user'
import {
  addPolicy,
  listPolicyActions
} from '@/api/policy'
import {
  listFolders
} from '@/api/folder'
export default {
  data () {
    return {
      users: [],
      folders: [],
      policy: {
        subject: '',
        object: '',
        action: '',
        effect: 'allow'
      },
      folderActions: [],
      loading: false,
      effect: ['allow', 'deny'],
      error: '',
      rules: {
        subject: [{
          required: true,
          message: 'Please select user',
          trigger: 'change'
        }],
        object: [{
          required: true,
          message: 'Object is required',
          trigger: 'change'
        }],
        action: [{
          required: true,
          message: 'Action is required',
          trigger: 'change'
        }],
        effect: [{
          required: true,
          message: 'Effect is required',
          trigger: 'change'
        }]
      }
    }
  },
  mounted () {
    listUsers().then((response) => {
      this.users = response.data
    })
    listPolicyActions().then((response) => {
      for (var i = 0; i < response.data.length; i++) {
        if (response.data[i].startsWith('folder:')) {
          this.folderActions.push(response.data[i])
        }
      }
    })
    listFolders('offset=0&limit=100').then((response) => {
      this.folders = response.data
    })
  },
  methods: {
    addPolicy () {
      this.loading = true
      var isValid = false
      this.$refs['add-policy-form'].validate((valid) => {
        isValid = valid
      })
      if (!isValid) {
        return
      }
      addPolicy(JSON.stringify(this.policy)).then((response) => {
        this.$message({
          message: 'Added policy successfully',
          type: 'success'
        })
      }).catch((error) => {
        this.$message({
          message: 'Failed to add policy: ' + error,
          type: 'error'
        })
      }).finally(() => {
        this.loading = false
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.policy {
  .title {
    font-size: 1.5em;
    font-weight: 700;
  }
}
</style>