<template>
  <h1>確認癖解消アプリ</h1>
  <div class="input-group mb-3">  
    <span class="input-group-text" id="basic-addon1">Edit:</span>
    <input type="text" class="form-control" v-model="textVal"/><button class="btn btn-primary addbutton" v-on:click="add">add</button><br />
  </div>


  <ul class="list-group">
        <li class="list-group-item"
            v-for="item in items" 
            v-bind:key="item.id"
        >{{item.text}}
        <button 
        class="btn btn-secondary okbutton"
        v-if="item.done"
        v-on:click="done(item.id)">OK</button>
        </li>
    </ul>
    <br />

    <ul class="list-group">
      <li class="list-group-item" v-for="stamp in stamps" v-bind="stamp.id">
        {{stamp.time}}
      </li>
  </ul>
  <br /><br />

  <div class="input-group mb-3">
    <span class="input-group-text" id="basic-addon1">Remove:</span>
    <button
    class="btn btn-primary"
    v-on:click="remove">remove
  </button></div>

  <div class="input-group mb-3">
    <span class="input-group-text" id="basic-addon1">Entry:</span>
    <button class="btn btn-dark" v-on:click="entry">entry</button>
  </div>

</template>

<script>
var listStorage = {
  fetch: function(obj) {
    let list = window.localStorage.getItem(obj)
    return list
  }
}

export default {
  name: 'Kakuninheki',
  props: {
    msg: String
  },
  data() {
    return {
      items: [],
      stamps: []
    }
  },
  created(){
    this.get()
  },
  mounted() {
    this.autoRemove()
  },
  methods:{
    get() {
        if (JSON.parse(listStorage.fetch('items')) !== null) {
            this.items = JSON.parse(listStorage.fetch('items'))
        } else {
            this.items = [{"id":1, "text":"Check", "done":false}]
        }
        if (JSON.parse(listStorage.fetch('stamps')) !== null) {
            this.stamps = JSON.parse(listStorage.fetch('stamps'))
        } else {
            this.stamps = [{"id":1, "time":"Time Stamp"}]
        }
    },
    add(){
        this.items.push({
            "id": this.items.length + 1,
            "text":this.textVal,
            "done":true,
        })
    },
    entry(){
        window.localStorage.setItem("items", JSON.stringify(this.items, null, 4))
        alert("登録しました")
    },
    autoRemove(){
      let yesterday = null
      if (listStorage.fetch('date') !== (null || undefined)) {
        yesterday = Number(listStorage.fetch('date'))
      } else {
        yesterday = Number(new Date().getDate())
      }
      let today = Number(new Date().getDate())
      if (yesterday !== today){
        window.localStorage.removeItem("items")
        this.items = [{"id":1, "text":"Check", "done":false}]
        window.localStorage.removeItem("stamps")
        this.stamps = [{"id":1, "time":"Time Stamp"}]
        window.localStorage.setItem("items", JSON.stringify(this.items, null, 4))
        window.localStorage.removeItem("stamps")
      }
    },
    remove(){
      if (window.confirm("すべて削除しますか?")) {
        window.localStorage.removeItem("items")
        this.items = [{"id":1, "text":"Check", "done":false}]
        window.localStorage.removeItem("stamps")
        this.stamps = [{"id":1, "time":"Time Stamp"}]

      }     
    },
    done(id){
        window.localStorage.setItem("date", new Date().getDate())

        if(this.items.length === id){
          let date = new Date() 
          this.stamps.push({
            "id": this.stamps.length + 1,
            "time":date.getHours() + "時" + date.getMinutes() + "分" + date.getSeconds() + "秒",
          })
          window.localStorage.setItem("stamps",  JSON.stringify(this.stamps, null, 4))
          for (let i = 1; i <= this.items.length; i++)
            this.items[i]["done"] = true
        }
        this.items[id - 1]["done"] = false
        this.itmes[0]["done"] = false
        window.localStorage.setItem("items", JSON.stringify(this.items, null, 4))

    }
  }
}
</script>