<template>
  <div v-if="list3 && list3.length > 0">
    <aplayer
      theme="#41b883"
      shuffle
      repeat="list"
      show-lrc
      :muted.sync="muted"
      :volume.sync="volume"
      :music="list3[0]"
      :list="list3"
    />
    <el-button type="text" @click="dialogVisible = true">Lấy nhạc từ google drive</el-button>

    <el-dialog
      title="Đồng bộ nhạc với google drive"
      :visible.sync="dialogVisible"
      :before-close="handleClose"
      width="30%"
    >
      <iframe src="/googledrive" title="W3Schools Free Online Web Tutorials" style="width: 100%" />
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="closeConnetGoogleDrive">Confirm</el-button>
      </span>
    </el-dialog>
  </div>

</template>

<script>
import Aplayer from 'vue-aplayer'
export default {
  name: 'Music',
  components: {
    Aplayer
  },
  data() {
    return {
      dialogVisible: false,
      volume: 1,
      muted: false,
      music3: null,
      list3: [],
      temp: [
        {
          title: 'Anh chỉ là người em từng yêu',
          artist: 'Khắc Anh',
          src: 'https://firebasestorage.googleapis.com/v0/b/developer-tool-b283b.appspot.com/o/music%2FAnh-Chi-La-Nguoi-Em-Tung-Yeu-Khac-Anh.mp3?alt=media&token=d2b160a0-03d4-4e3d-9d34-6ba2dd5bd6a7',
          pic: 'https://avatar-nct.nixcdn.com/singer/avatar/2016/01/25/4/1/1/7/1453716606229_600.jpg',
          lrc: 'https://lrc-nct.nixcdn.com/2015/06/11/2/2/1/a/1434016661699.lrc'
        },
        {
          title: 'Dừng Thương',
          artist: 'ĐatKaa',
          src: 'https://firebasestorage.googleapis.com/v0/b/developer-tool-b283b.appspot.com/o/music%2FDung-Thuong.mp3?alt=media&token=71b02cd6-3d1c-4e37-8f08-04444c60bd08',
          pic: 'https://avatar-nct.nixcdn.com/singer/avatar/2016/01/25/4/1/1/7/1453716606229_600.jpg',
          lrc: 'https://cn-east-17-aplayer-35525609.oss.dogecdn.com/hikarunara.lrc'
        },
        {
          title: 'Hơn cả yêu',
          artist: 'Đức Phúc',
          src: 'https://firebasestorage.googleapis.com/v0/b/developer-tool-b283b.appspot.com/o/music%2FHon-Ca-Yeu.mp3?alt=media&token=86daee96-11e4-4433-a96d-beba13c57ae2',
          pic: 'https://avatar-nct.nixcdn.com/singer/avatar/2016/01/25/4/1/1/7/1453716606229_600.jpg',
          lrc: 'https://cn-east-17-aplayer-35525609.oss.dogecdn.com/snowmoonflowers.lrc'
        }
      ]
    }
  },
  watch: {
    list3() {
      if (!this.list3) {
        this.list3 = this.temp
      }
    }
  },
  mounted() {
    this.list3 = JSON.parse(localStorage.getItem('listMusic')) ? JSON.parse(localStorage.getItem('listMusic')) : this.temp
  },
  methods: {
    closeConnetGoogleDrive() {
      this.dialogVisible = false
      this.list3 = JSON.parse(localStorage.getItem('listMusic'))
    },
    handleClose(done) {
      this.$confirm('Are you sure to close this dialog?')
        .then(_ => {
          this.list3 = JSON.parse(localStorage.getItem('listMusic'))
        })
        .catch(_ => {})
    }
  }
}
</script>

<style scoped>

</style>
