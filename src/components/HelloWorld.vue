<template>
  <div class="hello">
    <!-- <h3>{{ msg }}</h3> -->
    <el-input id="in1"
     type="textarea"
     clearable
    :autosize="{ minRows: 5, maxRows: 15}"
    placeholder="请输入645报文 68开头 16结尾 以空格分隔"
    style="font-size:20px;font-family:'Microsoft YaHei'"
    v-model="textarea">
    </el-input>
    <br>
    <br>
    <el-button type="primary" @click="btn_click">计算校验和</el-button>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
   data() {
    return {
      textarea: ''
    }
  },
  props: {
    msg: String
  },
  methods:
  {
    btn_click()
    {
        let inStr = this.textarea.trim();
        let hexs = inStr.split(" ");
        if(hexs[0] != '68')
        {
          this.$message.error('不是合法的报文 - 第一个68不对');
          return;
        }

        if(hexs[7] != '68'){
          this.$message.error('不是合法的报文 - 第二个68不对');
          return;

        }

        let len = hexs.length;
        if(hexs[len - 1] != '16')
        {
          this.$message.error('不是合法的报文 - 结束16不对');
          return;
        }

        let dataLen = Number('0x' + hexs[9])
        if(dataLen + 12 != len)
        {
          this.$message.error('不是合法的报文 - 总长度不对');
          return;
        }


        let lenFor = len - 2;
        var sum = 0;
        for(var i = 0;i < lenFor; i++)
        {
          let h =  '0x' + hexs[i];
          let s = Number(h);
          if(s > 256)
          {
             this.$message.error('不是合法的报文 - 有非法字节');
            return;
          }
          sum += s;
        }

        let cs = sum % 256;
        let str = cs.toString(16).toUpperCase();

        if(hexs[lenFor].toUpperCase() == str)
        {
          this.$message({
          message: '校验和正确 - ' + str,
          type: 'success'
          });
        return;
        }
        
        let oldCs =  hexs[lenFor].toUpperCase();
        hexs[lenFor] = str;

        var out = '';
        hexs.forEach(e => {
          
          out += e;
          out += " ";
        });


        this.textarea =   out;


      this.$message({
        message: '校验和已修证 ' +oldCs +" => "+ str,
        type: 'success'
        });
        

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.hello {
  width: 80%;
  margin:  0 auto;
}

h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}



</style>
