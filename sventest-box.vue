<template>
  <div class="login-wrap">
    <div class="signInfoContent">
      <div class="contentTop">
        <div class="topImg">
          <img src="../../assets/log_logo.png" style="width: 30%;" />
          <div id style></div>
        </div>
        <div class="contentCenter">
          <div class="box-add form-inline">
            <div class="form-group box-size">
              <label for="size_1">Box Size：</label>
              <input type="number" class="form-control input-number" id="size_1" value disabled />
              <input type="number" class="form-control input-number" id="size_2" value disabled />
            </div>
            <div class="box-symbol">
              <div class="box-symbol_type">
                <span class="box-symbol_tag box-type-S" data-type="S" draggable="true"></span>S
                <span class="box-symbol_tag box-type-M" data-type="M" draggable="true"></span>M
                <span class="box-symbol_tag box-type-L" data-type="L" draggable="true"></span>L
                <span class="box-symbol_tag box-type-XL" data-type="XL" draggable="true"></span>XL
                <span
                  class="box-symbol_tag box-type-console"
                  data-type="console"
                  draggable="true"
                ></span>console
              </div>
            </div>
          </div>

          <div class="box-buttons"></div>
          <table
            class="box-table"
            cellspacing="0"
            cellpadding="0"
            style="border-collapse: collapse;">
            <tbody></tbody>
          </table>
          <div class="edit-content">
				<div class="panel-footer text-center">
					<el-button @click="submit">Submit</el-button>
				</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import $ from "jquery";
export default {
  data: function() {
    return {
      box: {
        title: "",
        cabinets: [
          {
            id: "8a7eae856e20cc13016e20ea7f42000b",
            number: 1,
            mouths: [
              {
                id: "8a7eae856e20cc13016e20ea7f45000c",
                mouthType: {
                  id: "d092725609ab11e58bdb0242ac110001",
                  name: "S",
                  defaultUsePrice: 0,
                  defaultOverduePrice: 40,
                  deleteFlag: 0,
                  length: 0.0,
                  width: 54.0,
                  height: 29.0
                },
                useType: "OTHER",
                number: 1,
                numberInCabinet: 1,
                usePrice: 0,
                overduePrice: 40,
                status: "ENABLE",
                deleteFlag: 0,
                syncFlag: 1
              },
              {
                id: "8a7eae856e20cc13016e20ea7f4a000d",
                mouthType: {
                  id: "d092725609ab11e58bdb0242ac110001",
                  name: "S",
                  defaultUsePrice: 0,
                  defaultOverduePrice: 40,
                  deleteFlag: 0,
                  length: 0.0,
                  width: 54.0,
                  height: 29.0
                },
                useType: "OTHER",
                number: 2,
                numberInCabinet: 2,
                usePrice: 0,
                overduePrice: 40,
                status: "ENABLE",
                deleteFlag: 0,
                syncFlag: 1
              }
            ],
            deleteFlag: 0
          }
        ]
      },
      size_1: 10,
      size_2: 10,
      statusMap: ["ENABLE", "USED", "LOCKED"],
      controlPic: require("../../assets/control.png")
    };
  },
  created() {
    window.resetNumber = this.resetNumber;
    window.showColor = this.showColor;
    window.typeMap = {
      MINI: "d092749609ab11e58bdb0242ac110001",
      S: "d092725609ab11e58bdb0242ac110001",
      M: "d0926da009ab11e58bdb0242ac110001",
      L: "d09275cb09ab11e58bdb0242ac110001",
      XL: "d09276cb09ab11e58bdb0242ac110001",
      // XXL: 'd09277c509ab11e58bdb0242ac110001',
      STORE: "d09377c509ab11e58bdb0242ac110001",
      TAKE: "d09477c509ab11e58bdb0242ac110001",
      console: "d09577c509ab11e58bdb0242ac110001"
    };
    window.statusMap = ["ENABLE", "USED", "LOCKED"];
    window.typeReMap = {
      d092749609ab11e58bdb0242ac110001: "MINI",
      d092725609ab11e58bdb0242ac110001: "S",
      d0926da009ab11e58bdb0242ac110001: "M",
      d09275cb09ab11e58bdb0242ac110001: "L",
      d09276cb09ab11e58bdb0242ac110001: "XL",
      // 'd09277c509ab11e58bdb0242ac110001': 'XXL',
      d09377c509ab11e58bdb0242ac110001: "STORE",
      d09477c509ab11e58bdb0242ac110001: "TAKE",
      d09577c509ab11e58bdb0242ac110001: "console"
    };
    window.controlPic = this.controlPic;
  },
  mounted() {
    this.resetBox(this.box.cabinets);
    this.edit();
  },
  methods: {
    resetBox(data, size_1, size_2) {
      // size1代表列, size2代表行
      var self = this;
      size_1 = size_1 && size_1 > 10 ? size_1 : 10;
      size_2 = size_2 || 10;

      $("#size_1").val(size_1);
      $("#size_2").val(size_2);

      data = data || self.getAll();
      this.data = data;
      var html = "";
      for (var i = 0; i < size_1; i++) {
        html +=
          '<button style="visibility: hidden;" class="btn btn-sm btn-primary box-colcope box-add" data-index="' +
          i +
          '">c</button>' +
          '<button style="visibility: hidden;" class="btn btn-sm btn-primary box-colcope box-delcol" data-index="' +
          i +
          '">d</button>';
      }
      $(".box-buttons").html(html);
      html = "";
      var map = data.map(function() {
        return 0;
      });
      var boxData = [];
      data.map(function(item, ii) {
        if (item.deleteFlag != 1) {
          var mouthsresult = [];
          item.mouths.map(function(item2, iii) {
            if (item2.deleteFlag != 1) {
              mouthsresult.push(item2);
            }
          });
          //这里是实际格口一共有多少列
          if (mouthsresult.length) {
            boxData.push({
              id: item.id,
              number: item.number,
              deleteFlag: item.deleteFlag,
              mouths: mouthsresult
            });
          }
        }
      });

      for (var i = 0; i < size_2; i++) {
        var mouths = [],
          colids = [];
        boxData.map(function(item, ii) {
          if (item.mouths && item.mouths[map[ii]]) {
            if (item.mouths[map[ii]] && item.mouths[map[ii]].deleteFlag != 1) {
              mouths[ii] = item.mouths[map[ii]];
              colids.push(item.id || "");
              map[ii]++;
            }
          }
        });
        html += '<tr data-index="' + i + '">';

        for (var j = 0; j < size_1; j++) {
          var id = "",
            del = 0,
            number = "",
            type = "",
            status = "",
            express = "";
          if (mouths && mouths[j] && mouths[j].deleteFlag != 1) {
            id = mouths[j].id || "";
            del = mouths[j].deleteFlag || 0;
            number = mouths[j].number;

            status = window.statusMap.indexOf(mouths[j].status) || 0;
            type = window.typeReMap[mouths[j].mouthType.id];
            if (mouths[j].express) {
              if (!mouths[j].express.storeUser) {
                console.log(mouths);
              }
              var expressInfo = {
                expressNumber: mouths[j].express.expressNumber || "",
                storeTime: mouths[j].express.storeTime || "",
                takeTime: mouths[j].express.takeTime || "",
                validateCode: mouths[j].express.validateCode || "",
                storeUserName: mouths[j].express.storeUser
                  ? mouths[j].express.storeUser.name
                  : "no",
                storeUserPhoneNumber: mouths[j].express.storeUser
                  ? mouths[j].express.storeUser.phoneNumber
                  : "no",
                takeUserPhoneNumber:
                  mouths[j].express.takeUserPhoneNumber || "",
                expressstatus: mouths[j].express.status || "",
                expressId: mouths[j].express.id || ""
              };
              for (var x in expressInfo) {
                express += "data-" + x + '="' + expressInfo[x] + '" ';
              }
            }
          }
          var showType = type,
            showNumber = number;
          if (del) {
            (showType = ""), (showNumber = "");
          }
          html +=
            '<td style="border: 1px solid #ddd;" class="box-type-' +
            showType +
            '" data-colid="' +
            (colids[j] || "") +
            '" data-id="' +
            id +
            '" data-del="' +
            del +
            '" data-number="' +
            number +
            '" data-status="' +
            status +
            '" data-type="' +
            type +
            '" data-index="' +
            j +
            '" ' +
            express +
            ' draggable="true">\
                            <div class="box-number" data-number="' +
            number +
            '">' +
            showNumber +
            '</div>\
                            <div class="box-type" data-number="' +
            number +
            '">' +
            showType +
            '</div>\
                            <div class="box-info" data-number="' +
            number +
            '"><i class="glyphicon glyphicon-info-sign"></i></div>\
			<div class="box-op" style="display: none;position: relative;margin: -55px -25px;">\
				<button :class="btn btn-sm btn-primary box-cope">\
					<span>+</span>\
				</button>\
				<button class="btn btn-sm btn-primary box-del">\
					<span>-</span>\
				</button>\
			</div>\
			</td>';
        }
        html += "</tr>";
      }

      $(".box-table tbody").html(html);
      $(".box-table tr td").css({
        width: 830 / size_1 + "px",
        height: 60 + "px"
      });
      $(".box-colcope").css({
        width: 420 / size_1 + "px"
      });
      window.showColor();
    },
    getAll() {
      var result = [];
      var len = $(".box-table tr:first td").length;
      var numberInCabinet = 0;
      var delnumberInCabinet = 0;
      var anddelnumberInCabinet = 0;
      var n = 0;
      for (var i = 0; i < len; i++) {
        $(".box-table tr").each(function(k, e) {
          var td = $(e).find("td:eq(" + i + ")");
          if (!!td.data("type")) {
            if (!result[n]) {
              result[n] = {
                number: i + 1,
                mouths: []
              };
              td.data("colid") && (result[n].id = td.data("colid"));
            }
            if (k == 0) {
              numberInCabinet = 0;
              delnumberInCabinet = 0;
              anddelnumberInCabinet = 0;
            }
            var flag = "";
            if (td.data("del") == 1) {
              flag = true;
            } else {
              flag = false;
            }
            if (flag) {
              delnumberInCabinet = numberInCabinet + 1;
              numberInCabinet = delnumberInCabinet;
            } else {
              anddelnumberInCabinet = anddelnumberInCabinet + 1;
              numberInCabinet = anddelnumberInCabinet;
            }
            var mouth = {
              number: td.data("number"),
              numberInCabinet: numberInCabinet,
              mouthType: {
                id: window.typeMap[td.data("type")]
              },
              deleteFlag: td.data("del"),
              useType: "OTHER"
            };
            td.data("id") && (mouth.id = td.data("id"));
            result[n].mouths.push(mouth);
          }
        });
        n++;
      }
      var resNumber = 1;
      var res = result.map(function(e) {
        var deleteFlag = 1;
        e.mouths.map(function(ee) {
          if (ee.deleteFlag != 1) {
            deleteFlag = 0;
            return false;
          }
        });
        e.deleteFlag = deleteFlag;
        e.number = resNumber;
        if (!deleteFlag) {
          resNumber++;
        }
        return e;
      });
      return res;
    },
    edit() {
      var self = this;
      setTimeout(function() {
        $(".box-symbol_type").show();
        $(".edit-content").show();
        self.drag();
        self.cope();
      }, 400);
    },
    drag() {
      var self = this;
      $("#size_1, #size_2")
        .attr("disabled", false)
        .off("change", null)
        .on("change", function() {
          var size_1 = $("#size_1").val();
          var size_2 = $("#size_2").val();
          self.resetBox(null, size_1, size_2);
          self.drag();
          self.cope();
        });
      var typeRecord;
      $(".box-symbol_tag")
        .css("cursor", "move")
        .off("dragstart", null)
        .on("dragstart", function(e) {
          typeRecord = $(e.target).data("type");
        });

      $(".box-table td")
        .css("cursor", "move")
        .off("dragstart", null)
        .on("dragstart", function(e) {
          $(this)
            .find(".box-op")
            .hide();
          typeRecord = $(e.target).data("type");
        })
        .off("drop", null)
        .on("drop", function(e) {
          var type = typeRecord;
          var t = $(e.target);
          if (t[0].tagName != "TD") {
            t = t.parent();
          }
          t.data("type", type).data("del", "0");
          t.removeClass().addClass("box-type-" + type);
          t.find(".box-type").text(type);
          window.resetNumber();
          e.preventDefault();
        })
        .off("dragover", null)
        .on("dragover", function(e) {
          e.preventDefault();
        });
    },
    cope() {
      //复制整列
      $(".box-table td")
        .off("mouseover", null)
        .on("mouseover", function(e) {
          e.preventDefault();
          e.stopPropagation();
          if (!!$(this).data("type")) {
            if (!parseInt($(this).data("del"))) {
              //已删除的不显示左侧操作
              $(this)
                .find(".box-op")
                .show();
            }
            var index = $(this).data("index");
            $('.box-colcope[data-index="' + index + '"]').css(
              "visibility",
              "visible"
            );
          }
        })
        .off("mouseout", null)
        .on("mouseout", function(e) {
          e.preventDefault();
          e.stopPropagation();
          $(this)
            .find(".box-op")
            .hide();
          var index = $(this).data("index");
          $('.box-colcope[data-index="' + index + '"]').css(
            "visibility",
            "hidden"
          );
        });
      $(".box-colcope")
        .off("mouseover", null)
        .on("mouseover", function(e) {
          e.preventDefault();
          e.stopPropagation();
          $(this).css("visibility", "visible");
        });
      $(".box-colcope")
        .off("mouseout", null)
        .on("mouseout", function(e) {
          e.preventDefault();
          e.stopPropagation();
          $(this).css("visibility", "hidden");
        });
      $(".box-add")
        .off("click", null)
        .on("click", function(e) {
          //不要执行与事件关联的默认动作
          e.preventDefault();
          //停止事件传播
          e.stopPropagation();
          var index = $(this).data("index");
          var tdIndex = 1;
          $(".box-table tr:first td").each(function(ii, item2) {
            if (!$(item2).data("type")) {
              tdIndex = ii;
              return false;
            }
          });

          $(".box-table tr").each(function(i, item) {
            var itemTd = $(item).find("td:eq(" + index + ")");
            $(item)
              .find("td:gt(" + index + ")")
              .each(function(ii, item2) {
                if (
                  $(item2).data("index") == tdIndex &&
                  !parseInt(itemTd.data("del"))
                ) {
                  var type = itemTd.data("type");
                  $(item2)
                    .data("type", type)
                    .data("del", "0");
                  $(item2)
                    .removeClass()
                    .addClass("box-type-" + type);
                  $(item2)
                    .find(".box-type")
                    .text(type);
                  return false;
                }
              });
          });
          window.resetNumber();
        });

      //删除整列
      $(".box-table td")
        .off("mouseover", null)
        .on("mouseover", function(e) {
          e.preventDefault();
          e.stopPropagation();
          if (!!$(this).data("type")) {
            if (!parseInt($(this).data("del"))) {
              //已删除的不显示左侧操作
              $(this)
                .find(".box-op")
                .show();
            }
            var index = $(this).data("index");
            $('.box-colcope[data-index="' + index + '"]').css(
              "visibility",
              "visible"
            );
          }
        })
        .off("mouseout", null)
        .on("mouseout", function(e) {
          e.preventDefault();
          e.stopPropagation();
          $(this)
            .find(".box-op")
            .hide();
          var index = $(this).data("index");
          $('.box-colcope[data-index="' + index + '"]').css(
            "visibility",
            "hidden"
          );
        });
      $(".box-colcope")
        .off("mouseover", null)
        .on("mouseover", function(e) {
          e.preventDefault();
          e.stopPropagation();
          $(this).css("visibility", "visible");
        });
      $(".box-colcope")
        .off("mouseout", null)
        .on("mouseout", function(e) {
          e.preventDefault();
          e.stopPropagation();
          $(this).css("visibility", "hidden");
        });
      $(".box-delcol")
        .off("click", null)
        .on("click", function(e) {
          e.preventDefault();
          e.stopPropagation();
          var index = $(this).data("index");
          var tdIndex = 1;
          $(".box-table tr:first td").each(function(ii, item2) {
            if (!$(item2).data("type")) {
              tdIndex = ii;
              return false;
            }
          });

          $(".box-table tr").each(function(i, item) {
            var itemTd = $(item).find("td:eq(" + index + ")");
            if (!itemTd.data("id")) {
              itemTd.data("type", "");
            } else {
              itemTd.data("del", "1");
            }

            itemTd.removeClass();
            itemTd.find(".box-type").text("");
            itemTd.find(".box-number").text("");
            itemTd.find(".box-op").hide();
            window.resetNumber();
          });
          window.resetNumber();
        });

      //复制此列
      $(".box-table td .box-cope ")
        .off("click", null)
        .on("click", function(e) {
          e.preventDefault();
          e.stopPropagation();
          var trIndex = $(this)
            .parent()
            .parent()
            .parent()
            .data("index");
          var tdIndex = $(this)
            .parent()
            .parent()
            .data("index");
          var copeArr = [];
          var num = 0;
          $(".box-table tr").each(function(i, item) {
            var itemTd = $(item).find("td:eq(" + tdIndex + ")");
            if ($(item).data("index") <= trIndex) {
              copeArr.push({
                type: itemTd.data("type")
              });
            } else {
              if (!copeArr[num]) {
                return false;
              }
              if (!itemTd.data("type") || parseInt(itemTd.data("del"))) {
                //var number = copeArr[num].number;
                //var status = copeArr[num].status;
                var type = copeArr[num].type;
                itemTd.data("type", type).data("del", "0");
                itemTd.removeClass().addClass("box-type-" + type);
                itemTd.find(".box-type").text(type);
                num++;
              }
            }
          });
          window.resetNumber();
        });

      //删除此项
      $(".box-table td .box-del ").off("click", null).on("click", function(e) {
          e.preventDefault();
          e.stopPropagation();
          var itemTd = $(this).parent().parent();
          if (!itemTd.data("id")) {
            itemTd.data("type", "");
          } else {
            itemTd.data("del", "1");
          }

          itemTd.removeClass();
          itemTd.find(".box-type").text("");
          itemTd.find(".box-number").text("");
          itemTd.find(".box-op").hide();
          window.resetNumber();
        });
    },
    setStatus() {
      var numberArr = arr.map(function(e) {
        return e.number;
      });
      $(".box-table td").each(function(i, e) {
        if (
          $.inArray($(e).data("number"), numberArr) != -1 &&
          !parseInt($(e).data("del"))
        ) {
          $(e)
            .data("status", status)
            .removeClass("box-status-0")
            .removeClass("box-status-1")
            .removeClass("box-status-2")
            .addClass("box-status-" + status);
        }
      });
    },
    changeColorToStatus() {
      $(".box-table td").each(function(i, e) {
        if (!parseInt($(e).data("del"))) {
          if ($(e).data("status")) {
            $(e)
              .removeClass()
              .addClass("box-status-" + $(e).data("status"));
          } else if ($(e).data("number")) {
            $(e)
              .removeClass()
              .addClass("box-status-0");
          }
        }
      });
    },
    //给td的class渲染，动态生成的class不生效，需要重新渲染，还没找到方法优化
    showColor() {
      $(".box-table td").each(function(i, e) {
        if ($(e).hasClass("box-type-S")) {
          $(e).css("background-color", "#ffb54c");
        } else if ($(e).hasClass("box-type-M")) {
          $(e).css("background-color", "#f79f22");
        } else if ($(e).hasClass("box-type-L")) {
          $(e).css("background-color", "#bf7814");
        } else if ($(e).hasClass("box-type-XL")) {
          $(e).css("background-color", "#955c0c");
        } else if ($(e).hasClass("box-type-console")) {
          $(e).css("background-image", "url(" + window.controlPic + ")");
          $(e).css("background-size", "83px 60px");
        } else {
          $(e).css("background-color", "#fff");
        }

        //给元素加样式，动态添加的类都没生效，需要重写
        var firstChild = e.children[0];
        var secondChild = e.children[1];
        if (!!$(firstChild).data("number")) {
          $(firstChild).css({
            "font-size": "20px",
            color: "#fff",
            padding: "0 6px"
          });
        }
        if (!!$(secondChild).data("number")) {
          $(secondChild).css({
            "text-align": "right",
            padding: "0 6px",
            color: "#fff"
          });
        }
      });
    },
    resetNumber() {
      var number = 1;
      var len = $(".box-table tr:first td").length;
      //len是table有几列
      for (var i = 0; i < len; i++) {
        $(".box-table tr").each(function(k, e) {
          var td = $(e).find("td:eq(" + i + ")");
          if (
            !!td.data("type") &&
            !parseInt(td.data("del")) &&
            td.data("type") != "console"
          ) {
            td.data("number", number);
            td.find(".box-number").text(number);
            td.find("div").data("number", number);
            number++;
          } else if (
            !!td.data("type") &&
            !parseInt(td.data("del")) &&
            td.data("type") == "console"
          ) {
            td.data("number", 0);
            td.find(".box-number").text(0);
            td.find("div").data("number", 0);
          } else {
            if (!parseInt(td.data("del"))) {
              td.data("number", "");
              td.find("div").data("number", "");
            }
            td.find(".box-number").text("");
          }
        });
      }
      window.showColor();
	},
	//提交按鈕
	submit(){
		let cabinets = this.getAll();
		console.log(cabinets);
	}
  }
};
</script>

<style lang="scss" scoped>
.login-wrap {
  position: relative;
  width: 100%;
  height: 100%;
  background: white;
  overflow-y: scroll;
}

.signInfoContent {
  width: 1024px;
  margin: 60px auto;
  padding: 20px;
}

.contentTop {
  width: 100%;
  border: 1px solid gainsboro;
  .topImg {
    margin-top: 20px;
    img {
      padding-left: 20px;
      width: 30%;
    }
    div {
      height: 1px;
      background: gainsboro;
      margin-top: 20px;
    }
  }
}

.contentCenter {
  margin: 40px;
  .contentTitle {
    width: 100%;
    height: 40px;
    text-align: center;
    font-size: 20px;
    margin-bottom: 10px;
  }
  .form-box {
    width: 700px;
    margin: 0 auto;
    a {
      margin-left: 20px;
    }
    .el-input {
      width: 240px;
    }
  }
}

.contentfooter {
  width: 100%;
  margin: 20px 20px 0px 20px;
  padding-bottom: 20px;
  border-top: 1px solid gainsboro;
  .el-form-item__label {
    margin-left: 0px !important;
  }
}

//sven
.box-table tr td {
  width: 94px !important;
  height: 60px;
  border: 1px solid #ddd;
  vertical-align: top;
}

.box-table tr td.choose {
  box-shadow: 0 0 5px red;
}

.box-table tr td.selected {
  box-shadow: 0 0 5px #000;
}

.box-number {
  font-size: 20px;
  padding: 0 6px;
  color: #fff;
}

.box-type {
  text-align: right;
  padding: 0 6px;
  color: #fff;
}
.box-size {
  display: inline-block;
  width: 400px;
}

.box-size .input-number {
  width: 70px;
}

.box-symbol {
  display: inline-block;
}

.box-symbol_tag {
  display: inline-block;
  width: 30px;
  height: 30px;
  vertical-align: middle;
  margin: 5px;
}

.box-symbol_status {
  margin-left: 140px;
  display: none;
}

.box-type-MINI {
  background-color: #ffcd86;
}

.box-type-S {
  background-color: #ffb54c;
}

.box-type-M {
  background-color: #f79f22;
}

.box-type-L {
  background-color: #bf7814;
}

.box-type-XL {
  background-color: #955c0c;
}

.box-type-XXL {
  background-color: #5b3908;
}

.box-type-console {
  background: url(../../assets/control.png) no-repeat;
  background-size: 100% 100%;
}

.box-type-STORE {
  background-color: #955c0c;
}

.box-type-TAKE {
  background-color: #5b3908;
}

.box-info {
  display: none;
  position: absolute;
  margin: -48px 61px;
  color: #fff;
}

.box-info i {
  font-size: 12px;
  cursor: pointer;
}

#rect {
  position: absolute;
  border: 1px solid #bf343c;
}

.box-op {
  display: none;
  position: relative;
  margin: -55px -25px;
  width: 25px;
  height: 60px;
}

.box-op .btn {
  width: 25px;
  height: 30px;
  word-wrap: break-word;
  white-space: normal;
  font-size: 17px;
  line-height: 14px;
  padding: 0;
}

.box-op .btn .glyphicon {
  color: #fff;
}

.box-colcope {
  visibility: hidden;
  width: 47px !important;
  position: relative;
  margin-bottom: -1px;
}

.box-size {
  display: inline-block;
  width: 400px;
}

.box-size .input-number {
  width: 70px;
}

.box-symbol {
  display: inline-block;
}

.box-symbol_tag {
  display: inline-block;
  width: 30px;
  height: 30px;
  vertical-align: middle;
  margin: 5px;
}

.box-symbol_status {
  margin-left: 140px;
  display: none;
}

.table {
  width: 100%;
  max-width: 100%;
  margin-bottom: 21px;
}

.table > tbody > tr > th,
.table > tfoot > tr > th,
.table > thead > tr > th {
  padding: 8px;
  line-height: 1.52857143;
  vertical-align: top;
  border-bottom: 1px solid #eee;
}

.table > tbody > tr > td,
.table > tfoot > tr > td,
.table > thead > tr > td {
  padding: 8px;
  line-height: 43px;
  vertical-align: top;
  border-bottom: 1px solid #eee;
}

.table > thead > tr > th {
  vertical-align: bottom;
  border-bottom: 2px solid #eee;
}

.table > caption + thead > tr:first-child > td,
.table > caption + thead > tr:first-child > th,
.table > colgroup + thead > tr:first-child > td,
.table > colgroup + thead > tr:first-child > th,
.table > thead:first-child > tr:first-child > td,
.table > thead:first-child > tr:first-child > th {
  border-top: 0;
}

.table > tbody + tbody {
  border-top: 2px solid #eee;
}

.table .table {
  background-color: #f5f7fa;
}

@media screen and (max-width: 768px) {
  .signInfoContent {
    width: 90%;
  }
}
</style>