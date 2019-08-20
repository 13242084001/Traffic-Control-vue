<template>
  <div class="hello">
    <h1 style="background-color:#9b0909;color:black;padding:30px 20px">{{ msg }}</h1>
    <Collapse v-model="value2" accordion>
      <Panel name="1" hide-arrow>
        状态
        <div slot="content">
          <div>
            <Row>
              <Col span="8">
                <Card style="width:320px;border-bottom: white" ::bordered="false" shadow dis-hover>
                  <div style="text-align:center">

                    <h3>The Controlled Address</h3>

                    <div v-if="controlled_ip_list.length > 0">
                      <p style="padding-left: 60px" v-if="controlled_ip_list"
                         v-for="controlled_ip_dict in controlled_ip_list">
                        <span style="margin-right: 10px;cursor: pointer" @click="dele(controlled_ip_dict.controlled_ip)"><i class="fa fa-times" aria-hidden="true"></i></span>
                     <span style="cursor: pointer" @click="action(controlled_ip_dict.controlled_ip)">
                       {{ controlled_ip_dict.controlled_ip }}
                     </span>
                        <!--要记得动态更改css样式-->
                        <span v-if="controlled_ip_dict.status === true"
                              style="font-size: 5px;float: right;color: #1d9d74;cursor: pointer" @click="shaping()"><i
                          class="fa fa-circle" aria-hidden="true"></i></span>
                        <span v-else style="font-size: 5px;float: right;color: #E55457;cursor: pointer"><i
                          class="fa fa-circle" aria-hidden="true"></i></span>
                      </p>
                    </div>

                    <p v-else style="margin-left: 60px">You have no dev to shaping</p>
                  </div>
                </Card>
              </Col>
	      <Col span="8">
		echarts
	      </COl>
              <Col span="8">
                <!--<h2>Your Device Address</h2>
                <h1>{{ devIp }}</h1>-->
		<div style="background-color:black;color:white">
		  <p>&nbsp;&nbsp;interface: eth0</p>
		  <p>&nbsp;&nbsp;IP Address is: 192.168.0.10</p>
		  <p><div style="margin-left:10px;margin-right:10px;overflow:hidden;text-overflow:clib;white-space:nowrap">================================================================================================================================================================================================</div></p>
		  <p><Row><Col span="13">&nbsp;</Col><Col span="4">last2sec</Col><Col span="4">last5sec</Col><Col span="3">last10sec</Col></Row></p>
		  <p><Row><Col span="6">&nbsp;&nbsp;192.168.0.10</Col><Col span="7">==>&nbsp;&nbsp;{{ mcu_ip }}</Col><Col span="4">{{ last2sec_out }}kbps</Col><Col span="4">{{ last5sec_out }}kbps</Col><Col span="3">{{ last10sec_out }}kbps</Col></Row></p>
		  <p><Row><Col span="6">&nbsp;&nbsp;</Col><Col span="7"><==</Col><Col span="4">{{ last2sec_in }}kbps</Col><Col span="4">{{ last5sec_in }}kbps</Col><Col span="3">{{ last10sec_in }}kbps</Col></Row></p>
		  <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</p>
		</div>
              </Col>
            </Row>
          </div>
        </div>
      </Panel>
      <Panel name="2" hide-arrow>
        配置
        <div slot="content">
          <Row :gutter="30">
            <Col span="6">
              <h3 style="text-align: left;margin-bottom: 40px;font-weight: bold;">filter(过滤规则)</h3>
              <RadioGroup type="button" size="large" style="margin-bottom: 20px;font-weight: 500" v-model="rule.protocol">
                <Radio label="All"></Radio>
                <Radio label="tcp"></Radio>
                <Radio label="udp"></Radio>
              </RadioGroup>

              <Form :model="rule"  inline :label-width="50" class="form_font">

                <FormItem label="src">
                  <Input type="text" v-model="rule.local_ip" style="width: 100px;">
                  </Input>
                </FormItem>
                <FormItem label="sports">
                  <Input type="text" v-model="rule.local_port" style="width: 100px;">
                  </Input>
                </FormItem>
                <FormItem label="dst">
                  <Input type="text" v-model="rule.remote_ip" style="width: 100px;">
                  </Input>
                </FormItem>
                <FormItem label="dports">
                  <Input type="text" v-model="rule.remote_port" style="width: 100px;">
                  </Input>
                </FormItem>
              </Form>

            </Col>
            <Col span="9">
              <h2 style="font-weight: bold">upstream(上行)</h2>
              <Form :model="upstream" label-position="right" :label-width="200" class="form_font">
                <Divider orientation="left">Bandwidth(带宽)</Divider>
                <FormItem label="Rate">
                  <Input v-model="upstream.rate"></Input>
                </FormItem>
                <Divider orientation="left">Delay(延时)</Divider>
                <FormItem label="delay">
                  <Input v-model="upstream.delay.delay"></Input>
                </FormItem>
                <FormItem label="jitter(抖动)">
                  <Input v-model="upstream.delay.jitter"></Input>
                </FormItem>

                <Divider orientation="left">Loss(丢包)</Divider>
                <FormItem label="Percentage(百分比)">
                  <Input v-model="upstream.loss.percentage"></Input>
                </FormItem>
                <FormItem label="Correlation(成功率)">
                  <Input v-model="upstream.loss.correlation"></Input>
                </FormItem>
                <Divider orientation="left">Corruption(畸形包)</Divider>
                <FormItem label="Percentage(百分比)">
                  <Input v-model="upstream.corruption.percentage"></Input>
                </FormItem>
                <Divider orientation="left">Reoder(重传)</Divider>
                <FormItem label="Percentage(百分比)">
                  <Input v-model="upstream.reorder.percentage"></Input>
                </FormItem>
              </Form>
            </Col>
            <Col span="9">
              <h2 style="font-weight: bold">downstream(下行)</h2>
              <Form :model="downstream" label-position="right" :label-width="200" class="form_font">
                <Divider orientation="left">Bandwidth(带宽)</Divider>
                <FormItem label="Rate">
                  <Input v-model="downstream.rate"></Input>
                </FormItem>
                <Divider orientation="left">Delay(延时)</Divider>
                <FormItem label="delay">
                  <Input v-model="downstream.delay.delay"></Input>
                </FormItem>
                <FormItem label="jitter(抖动)">
                  <Input v-model="downstream.delay.jitter"></Input>
                </FormItem>

                <Divider orientation="left">Loss(丢包)</Divider>
                <FormItem label="Percentage(百分比)">
                  <Input v-model="downstream.loss.percentage"></Input>
                </FormItem>
                <FormItem label="Correlation(成功率)">
                  <Input v-model="downstream.loss.correlation"></Input>
                </FormItem>
                <Divider orientation="left">Corruption(畸形包)</Divider>
                <FormItem label="Percentage(百分比)">
                  <Input v-model="downstream.corruption.percentage"></Input>
                </FormItem>
                <Divider orientation="left">Reoder(重传)</Divider>
                <FormItem label="Percentage(百分比)">
                  <Input v-model="upstream.reorder.percentage"></Input>
                </FormItem>
              </Form>
            </Col>
            <Button v-if="doit === 'UPDATE'" type="info" long v-on:click="shaping(rule.local_ip)">{{ doit }}</Button>
            <Button v-else type="success" long v-on:click="shaping(rule.local_ip)">{{ doit }}</Button>
          </Row>
        </div>
      </Panel>
      <Panel name="3" hide-arrow>
        规则模板
        <p slot="content">
          功能开发中。。。</p>
      </Panel>
    </Collapse>
    <Row>
      <Col span="6">
        <json-viewer
          :value="rule"
          :expand-depth=5
          copyable
          boxed
          style="text-align: left"></json-viewer>
      </Col>
      <Col span="9">
        <json-viewer
          :value="upstream"
          :expand-depth=5
          copyable
          boxed
          style="text-align: left"></json-viewer>
      </Col>
      <Col span="9">
        <json-viewer
          :value="downstream"
          :expand-depth=5
          copyable
          boxed
          style="text-align: left">

        </json-viewer>
      </Col>
    </Row>

  </div>
</template>

<script>

  export default {
    name: 'HelloWorld',
    data() {
      return {
        msg: 'Traffic Control for HST',
        msg1: null,
        doit: "SUBMIT",
        value2: '1',
        devIp: null,
        controlled_ip_list: [
          //{"controlled_ip": "192.168.1.3", "status": "on"},
          //{"controlled_ip": "171.16.1.2", "status": "off"}
        ],

	last2sec_out: null,
	last5sec_out: null,
	last10sec_out: null,
	mcu_ip: null,

        rule: {
          protocol: "All",
          local_ip: '',
          local_port: '',
          remote_ip: '',
          remote_port: ''
        },
        upstream: {
          rate: '',
          delay: {
            delay: '',
            jitter: '',
          },
          loss: {
            percentage: '',
            correlation: '',
          },
          corruption: {
            percentage: ''
          },
          reorder: {
            percentage: ''
          }
        },
        downstream: {
          rate: '',
          delay: {
            delay: '',
            jitter: '',
          },
          loss: {
            percentage: '',
            correlation: '',
          },
          corruption: {
            percentage: ''
          },
          reorder: {
            percentage: ''
          }
        },

      }
    },
    methods: {
      getDevIp() {
        var that = this;
        this.$axios.request({
          url: process.env.URL_PATH+"/getDevIp",
          method: "get"
        }).then(function (ret) {
          console.log(ret);
          console.log(ret.data);
          if (ret.status === 200) {
            that.devIp = ret.data.client_ip;
            that.controlled_ip_list = ret.data.controlled_ip_list;
	    that.last2sec_out = ret.data.avg2[1];
	    that.last5sec_out = ret.data.avg5[1];
	    that.last10sec_out = ret.data.avg10[1];
	    that.last2sec_in = ret.data.avg2[0];
	    that.last5sec_in = ret.data.avg5[0];
	    that.last10sec_in = ret.data.avg10[0];
	    that.mcu_ip = ret.data.mcu_ip;
          }
        })
      },
      shaping(ip) {
        console.log(ip);
        var that =this;
        if(this.doit === "UPDATE"){
          this.$axios.request({
            url: process.env.URL_PATH+"/shaping/" + ip,
            method: "put",
            params: {},
            data: {
              rule: that.rule,
              upstream: that.upstream,
              downstream: that.downstream
            }
          }).then(function (ret) {
            if(ret.status === 200){
              console.log("success");
              that.$Message.success("更新成功！")
            }
          })
        }else (
          this.$axios.request({
          url: process.env.URL_PATH+"/shaping/" + ip,
          method: "post",
          params: {},
          data: {
            rule: that.rule,
            upstream: that.upstream,
            downstream: that.downstream
          }
        }).then(function (ret) {
          console.log(ret);
          that.getDevIP();
            that.$Message.success("添加成功！")
        })
        )

      },
      // 查询一条设备数据
      action(ip) {
        var that = this;
        that.$axios.request({
          url: process.env.URL_PATH+"/shaping/",
          method: 'get'
        }).then(function (ret) {
          console.log(ret);
          console.log(ret.data);
          that.rule = ret.data.rule;
          that.upstream = ret.data.upstream;
          that.downstream = ret.data.downstream;
          that.doit = "UPDATE"
        })

      },
      //删除一个设备
      dele(ip) {
        var that = this;
        that.$axios.request({
          url: process.env.URL_PATH+"/shaping/",
          method: 'delete'
        }).then(function (ret) {
          if(ret.status === 200) {
            that.$Message.success("删除成功！");
            that.getDevIP();
          }
        })
      }


    },
    //mounted() {
      //this.getDevIp();
      //let msg1 = that.formatJson(row.value)
    //}
    mounted() {
      if (this.timer) {
        clearInterval(this.timer)
      } else {
        var that = this;
        this.timer = setInterval(function () {
          that.getDevIp();
        }, 2000);
      }
    },

    beforeDestroy() {
      clearInterval(this.timer);
      this.timer = null;
    }

  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1, h2 {
    font-weight: normal;
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

  p {
    text-align: left;
  }

  .my-awesome-json-theme {
    background: #fff;
    white-space: nowrap;
    color: #525252;
    font-size: 14px;
    font-family: Consolas, Menlo, Courier, monospace;

  .jv-ellipsis {
    color: #999;
    background-color: #eee;
    display: inline-block;
    line-height: 0.9;
    font-size: 0.9em;
    padding: 0px 4px 2px 4px;
    border-radius: 3px;
    vertical-align: 2px;
    cursor: pointer;
    user-select: none;
  }

  .jv-button {
    color: #49b3ff
  }

  .jv-key {
    color: #111111
  }

  .jv-item {

  &
  .jv-array {
    color: #111111
  }

  &
  .jv-boolean {
    color: #fc1e70
  }

  &
  .jv-function {
    color: #067bca
  }

  &
  .jv-number {
    color: #fc1e70
  }

  &
  .jv-object {
    color: #111111
  }

  &
  .jv-undefined {
    color: #e08331
  }

  &
  .jv-string {
    color: #42b983;
    word-break: break-word;
    white-space: normal;
  }

  }
  .jv-code {

  .jv-toggle {

  &
  :before {
    padding: 0px 2px;
    border-radius: 2px;
  }

  &
  :hover {

  &
  :before {
    background: #eee;
  }

  }
  }
  }
  }

  .form_font >>> .ivu-form-item-label{
    font-size: 15px;
    font-weight: bold;
  }


</style>
