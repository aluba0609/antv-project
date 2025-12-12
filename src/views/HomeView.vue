<template>
  <a-layout>
    <a-layout-header style="position: fixed; z-index: 1; width: 100%;background-color: #fff;padding: 0 10%;">
      <div style="display: flex;align-items: center;justify-content: space-between;">
        <div style="color: #165dff;font-size: 25px;font-weight: bold;">MES数据一致性比对平台</div>
        <div>最后刷新时间</div>
      </div>
    </a-layout-header>
    <a-layout-content style="padding: 0 10%; margin-top: 64px;">
      <!-- 筛选和控制区 -->
      <div>
        <a-card style="margin: 25px 0;box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);">
          <a-form name="searchForm" style="display: flex;align-items: center;justify-content: space-between;gap: 5px;"
            layout="inline" :model="searchFormState" autocomplete="off">

            <a-form-item name="factory" label="长别">
              <a-select v-model:value="searchFormState.factory" style="width:180px;">
                <template v-for="el in factoryList">
                  <a-select-option :value="el.value">{{ el.label }}</a-select-option>
                </template>
              </a-select>
            </a-form-item>

            <a-form-item name="msg" label="消息">
              <a-select mode="multiple" v-model:value="searchFormState.msg" style="width:180px;">
                <template v-for="el in msgList">
                  <a-select-option :value="el.value">{{ el.label }}</a-select-option>
                </template>
              </a-select>
            </a-form-item>

            <a-form-item name="line" label="线体">
              <a-select mode="multiple" v-model:value="searchFormState.line" style="width:180px;">
                <template v-for="el in lineList">
                  <a-select-option :value="el.value">{{ el.label }}</a-select-option>
                </template>
              </a-select>
            </a-form-item>

            <a-form-item name="cst" label="CST">
              <a-select mode="multiple" v-model:value="searchFormState.cst" style="width:200px;">
                <template v-for="el in cstList">
                  <a-select-option :value="el.value">{{ el.label }}</a-select-option>
                </template>
              </a-select>
            </a-form-item>

            <a-form-item name="time" label="周期">
              <a-range-picker v-model:value="searchFormState.time" :presets="rangePresets" style="width:250px;" />
            </a-form-item>

            <a-form-item>
              <a-button type="primary" @click="search">自动刷新中： 3s </a-button>
            </a-form-item>
          </a-form>
        </a-card>
      </div>

      <!-- 核心指标卡片区 -->
      <div>
        <div style="display: flex;justify-content: space-between;align-items: center;">
          <a-card style="box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);width: 24%;">
            <div
              style="display: flex;justify-content: space-between;align-items: flex-start;margin-bottom: 10px;flex-direction: column;">
              <div
                style="display: flex;flex-wrap: wrap;align-items: center;justify-content: space-between;width: 100%;">
                <div>
                  <div style="font-weight: 500;">消息准确率</div>
                  <div style="font-weight: 700;font-size: 25px;margin-top: 2px;">99.23%</div>
                </div>
                <div
                  style="border-radius: 9999px;width: 40px;height: 40px;background-color: #e7eeff;display: flex;justify-content: center;align-items: center;">
                  <MessageOutlined style="font-size: 20px;color: #165dff;" />
                </div>
              </div>
              <div style="margin-left: auto;">已比对: 1,286 条</div>
            </div>
          </a-card>

          <a-card style="box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);width: 24%;">
            <div
              style="display: flex;justify-content: space-between;align-items: flex-start;margin-bottom: 10px;flex-direction: column;">
              <div
                style="display: flex;flex-wrap: wrap;align-items: center;justify-content: space-between;width: 100%;">
                <div>
                  <div style="font-weight: 500;">CST准确率</div>
                  <div style="font-weight: 700;font-size: 25px;margin-top: 2px;">99.23%</div>
                </div>
                <div
                  style="border-radius: 9999px;width: 40px;height: 40px;background-color: #e7eeff;display: flex;justify-content: center;align-items: center;">
                  <AppstoreOutlined style="font-size: 20px;color: #165dff;" />
                </div>
              </div>
              <div style="margin-left: auto;">共比对: 1,286 条</div>
            </div>
          </a-card>

          <a-card style="box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);width: 24%;">
            <div
              style="display: flex;justify-content: space-between;align-items: flex-start;margin-bottom: 10px;flex-direction: column;">
              <div
                style="display: flex;flex-wrap: wrap;align-items: center;justify-content: space-between;width: 100%;">
                <div>
                  <div style="font-weight: 500;">线体准确率</div>
                  <div style="font-weight: 700;font-size: 25px;margin-top: 2px;">99.23%</div>
                </div>
                <div
                  style="border-radius: 9999px;width: 40px;height: 40px;background-color: #e7eeff;display: flex;justify-content: center;align-items: center;">
                  <ApartmentOutlined style="font-size: 20px;color: #165dff;" />
                </div>
              </div>
              <div style="margin-left: auto;">涉及: 1,286 条</div>
            </div>
          </a-card>

          <a-card style="box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);width: 24%;">
            <div
              style="display: flex;justify-content: space-between;align-items: flex-start;margin-bottom: 10px;flex-direction: column;">
              <div
                style="display: flex;flex-wrap: wrap;align-items: center;justify-content: space-between;width: 100%;">
                <div>
                  <div style="font-weight: 500;">表整体准确率</div>
                  <div style="font-weight: 700;font-size: 25px;margin-top: 2px;">99.23%</div>
                </div>
                <div
                  style="border-radius: 9999px;width: 40px;height: 40px;background-color: #e7eeff;display: flex;justify-content: center;align-items: center;">
                  <CarryOutOutlined style="font-size: 20px;color: #165dff;" />
                </div>
              </div>
              <div style="margin-left: auto;">已核对: 1,286 条</div>
            </div>
          </a-card>

        </div>
      </div>

      <div>
        <a-card style="margin: 25px 0;box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);">
          <div>
            <div style="display: flex;justify-content: space-between;margin-bottom: 10px;">
              <div style="font-size: 18px;font-weight: 700;">整体趋势分析</div>
              <div>
                <a-radio-group v-model:value="echartRange" button-style="solid" @change="echartRangeChane">
                  <a-radio-button value="day">日</a-radio-button>
                  <a-radio-button value="manth">月</a-radio-button>
                </a-radio-group>
              </div>
            </div>
            <div ref="chartDom" style="width: 100%; height: 400px"></div>
          </div>
        </a-card>
      </div>

      <div style="box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);">
        <a-table :columns="accuracyMatrixColumns" :data-source="accuracyMatrixData" bordered :pagination="false" class="accuracyMatrixClass">
          <template #bodyCell="{ text, record, index, column }">
            <template v-if="column.dataIndex === 'msgType'">
              <div style="padding: 16px;">{{ record.msgType }}</div>
            </template>
            <template v-if="column.title === 'D3TPHT'">
              <div style="text-align: center; background: linear-gradient(135deg, rgba(0, 180, 42, 0.2), rgba(0, 180, 42, 0.1));padding: 16px;" @click="() => openMetricsDetailModal(record)">
                <div style="color:#00B42A">{{ record.lines[column.dataIndex] }}%</div>
                <div>{{ record.counts[column.dataIndex] }}次</div>
              </div>
            </template>
            <template v-if="column.title === 'D3TICL'">
              <div style="text-align: center; background: linear-gradient(135deg, rgba(0, 180, 42, 0.2), rgba(0, 180, 42, 0.1));padding: 16px;" @click="() => openMetricsDetailModal(record)">
                <div style="color:#00B42A">{{ record.lines[column.dataIndex] }}%</div>
                <div>{{ record.counts[column.dataIndex] }}次</div>
              </div>
            </template>
            <template v-if="column.title === 'D3TSTR'">
              <div style="text-align: center; background: linear-gradient(135deg, rgba(0, 180, 42, 0.2), rgba(0, 180, 42, 0.1));padding: 16px;" @click="() => openMetricsDetailModal(record)">
                <div style="color:#00B42A">{{ record.lines[column.dataIndex] }}%</div>
                <div>{{ record.counts[column.dataIndex] }}次</div>
              </div>
            </template>
            <template v-if="column.title === 'D3TGLC'">
              <div style="text-align: center; background: linear-gradient(135deg, rgba(0, 180, 42, 0.2), rgba(0, 180, 42, 0.1));padding: 16px;" @click="() => openMetricsDetailModal(record)">
                <div style="color:#00B42A">{{ record.lines[column.dataIndex] }}%</div>
                <div>{{ record.counts[column.dataIndex] }}次</div>
              </div>
            </template>
            <template v-if="column.title === 'D3TCVD'">
              <div style="text-align: center; background: linear-gradient(135deg, rgba(0, 180, 42, 0.2), rgba(0, 180, 42, 0.1));padding: 16px;" @click="() => openMetricsDetailModal(record)">
                <div style="color:#00B42A">{{ record.lines[column.dataIndex] }}%</div>
                <div>{{ record.counts[column.dataIndex] }}次</div>
              </div>
            </template>
            <template v-if="column.title === 'D3TTHS'">
              <div style="text-align: center; background: linear-gradient(135deg, rgba(0, 180, 42, 0.2), rgba(0, 180, 42, 0.1));padding: 16px;" @click="() => openMetricsDetailModal(record)">
                <div style="color:#00B42A">{{ record.lines[column.dataIndex] }}%</div>
                <div>{{ record.counts[column.dataIndex] }}次</div>
              </div>
            </template>
          </template>
          <template #footer>
            <div>
              <!-- 图例说明 -->
              <div style="display: flex;flex-wrap: wrap; gap: 4px">
                <div style="display: flex;align-items: center;"><span
                    style="width: 15px;height: 15px;background-color: #cfedd8;margin-right: 5px; border: 1px solid #15b139;"></span>≥95%（优秀）
                </div>
                <div style="display: flex;align-items: center;"><span
                    style="width: 15px;height: 15px;background-color: #dcf1e3;margin-right: 5px;border: 1px solid #15b139;"></span>90%-94%（良好）
                </div>
                <div style="display: flex;align-items: center;"><span
                    style="width: 15px;height: 15px;background-color: #f8e5d3;margin-right: 5px;border: 1px solid #ff7d00;"></span>85%-89%（中等）
                </div>
                <div style="display: flex;align-items: center;"><span
                    style="width: 15px;height: 15px;background-color: #f8eadd;margin-right: 5px;border: 1px solid #ff7d00;"></span>80%-84%（中低）
                </div>
                <div style="display: flex;align-items: center;"><span
                    style="width: 15px;height: 15px;background-color: #f7dedf;margin-right: 5px;border: 1px solid #f53f3f;"></span>&lt;80%（低）
                </div>
              </div>
            </div>
          </template>
        </a-table>
      </div>

      <div style="margin: 25px 0;background-color: #fff;box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);">
        <div style="display: flex;justify-content: space-between;align-items: center;padding: 20px;">
          <div style="font-size: 18px;font-weight: 700;">详细比对结果</div>
          <div><a-input v-model:value="comparisonSearchVal" placeholder="搜索消息ID/批次号..."
              @change="comparisonSearchValChange" />
          </div>
        </div>
        <div>
          <a-table :columns="comparisonTableColumns" :data-source="comparisonTableData" bordered>
            <template #bodyCell="{ text, record, index, column }">
              <template v-if="column.dataIndex === 'msgResult'">
                <div
                  style="text-align: center;background-color: #e1e9fb;width: 60px;height: 20px;border-radius: 9999px;margin: auto;">
                  <div style="color:#00B42A">{{ record.msgResult }}</div>
                </div>
              </template>
              <template v-if="column.dataIndex === 'cstResult'">
                <div
                  style="text-align: center;background-color: #e1e9fb;width: 60px;height: 20px;border-radius: 9999px;margin: auto;">
                  <div style="color:#00B42A">{{ record.cstResult }}</div>
                </div>
              </template>
              <template v-if="column.dataIndex === 'normalFields'">
                <div
                  style="text-align: center;background-color: #e1e9fb;width: 60px;height: 20px;border-radius: 9999px;margin: auto;">
                  <div style="color:#00B42A">{{ record.normalFields }}</div>
                </div>
              </template>
              <template v-if="column.dataIndex === 'accuracyMap'">
                <div style="text-align: center;">
                  <div style="color:#00B42A">{{ record.accuracyMap }}</div>
                </div>
              </template>
              <template v-if="column.dataIndex === 'action'">
                <div style="text-align: center;color: #3cb0ff;cursor: pointer;"
                  @click="() => openMessageDetailModal(record)">
                  查看详情 >
                </div>
              </template>
            </template>
          </a-table>
        </div>
      </div>
    </a-layout-content>
  </a-layout>

  <a-modal v-model:open="metricsDetailVisibile" :title="metricsDetailTitle" style="width: 1000px;" :footer="null">
    <div style="display: flex;flex-wrap: wrap;">
      <div style="width: 35%;">
        <div style="margin: 10px 0;">基础统计</div>
        <a-form :model="openMetricsDetailForm" name="openMetricsDetail" :noStyle="true">
          <a-form-item label="消息类型" name="msgType">
            <div style="text-align: end;">{{ openMetricsDetailForm.msgType }}</div>
          </a-form-item>
          <a-form-item label="生产线" name="line">
            <div style="text-align: end;">{{ openMetricsDetailForm.line }}</div>
          </a-form-item>
          <a-form-item label="平均准确率" name="accuracy">
            <div style="text-align: end;">{{ openMetricsDetailForm.accuracy }}</div>
          </a-form-item>
          <a-form-item label="总比对次数" name="total">
            <div style="text-align: end;">{{ openMetricsDetailForm.total }}</div>
          </a-form-item>
          <a-form-item label="正确次数" name="match">
            <div style="text-align: end;"> {{ openMetricsDetailForm.match }}</div>
          </a-form-item>
          <a-form-item label="错误次数" name="mismatch">
            <div style="text-align: end;">{{ openMetricsDetailForm.mismatch }}</div>
          </a-form-item>
        </a-form>
      </div>
      <div style="width: 65%;">
        <div style="margin: 10px 0;">准确率拆分</div>
        <div ref="chartDom1" style="width: 100%; height: 350px"></div>
      </div>
    </div>
    <div>
      <div style="margin: 10px 0;">最近比对记录</div>
      <a-table :columns="metricsDetailTableColumns" :data-source="metricsDetailTableData" bordered :scroll="{ y: 300 }">
        <template #bodyCell="{ text, record, index, column }">
          <template v-if="column.dataIndex === 'msgResult'">
            <div
              style="text-align: center;background-color: #e1e9fb;width: 60px;height: 20px;border-radius: 9999px;margin: auto;">
              <div style="color:#00B42A">{{ record.msgResult }}</div>
            </div>
          </template>
          <template v-if="column.dataIndex === 'cstResult'">
            <div
              style="text-align: center;background-color: #e1e9fb;width: 60px;height: 20px;border-radius: 9999px;margin: auto;">
              <div style="color:#00B42A">{{ record.cstResult }}</div>
            </div>
          </template>
          <template v-if="column.dataIndex === 'normalFields'">
            <div
              style="text-align: center;background-color: #e1e9fb;width: 60px;height: 20px;border-radius: 9999px;margin: auto;">
              <div style="color:#00B42A">{{ record.normalFields }}</div>
            </div>
          </template>
          <template v-if="column.dataIndex === 'accuracyMap'">
            <div style="text-align: center;">
              <div style="color:#00B42A">{{ record.accuracyMap }}</div>
            </div>
          </template>
          <template v-if="column.dataIndex === 'action'">
            <div style="text-align: center;color: #3cb0ff;cursor: pointer;">
              查看详情 >
            </div>
          </template>
        </template>
      </a-table>
    </div>
  </a-modal>

  <a-modal v-model:open="messageDetailVisibile" :title="messageDetailTitle" style="width: 1000px;" :footer="null">
    <div>
      <a-form layout="vertical" :model="openMetricsDetailForm" name="openMessageDetail"  class="sysMsgFormClass">
        <a-row :gutter="24">
          <a-col :span="6">
            <a-form-item label="消息ID" name="msgType">
              <div style="">{{ openMetricsDetailForm.msgType }}</div>
            </a-form-item>
          </a-col>
          <a-col :span="6">
            <a-form-item label="批次号" name="line">
              <div style="">{{ openMetricsDetailForm.line }}</div>
            </a-form-item>
          </a-col>
          <a-col :span="6">
            <a-form-item label="线体" name="accuracy">
              <div style="">{{ openMetricsDetailForm.accuracy }}</div>
            </a-form-item>
          </a-col>
          <a-col :span="6">
            <a-form-item label="时间戳" name="total">
              <div style="">{{ openMetricsDetailForm.total }}</div>
            </a-form-item>
          </a-col>
          <a-col :span="6">
            <a-form-item label="比对时间" name="match">
              <div style=""> {{ openMetricsDetailForm.match }}</div>
            </a-form-item>
          </a-col>
          <a-col :span="6">
            <a-form-item label="设计数据表" name="mismatch">
              <div style="">{{ openMetricsDetailForm.mismatch }}</div>
            </a-form-item>
          </a-col>
          <a-col :span="6">
            <a-form-item label="权重折算平均正确率" name="mismatch">
              <div style="">{{ openMetricsDetailForm.mismatch }}</div>
            </a-form-item>
          </a-col>
          <a-col :span="6">
            <a-form-item label="比对结果" name="mismatch">
              <div style="">{{ openMetricsDetailForm.mismatch }}</div>
            </a-form-item>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <div>
      <div style="margin: 10px 0;font-weight: 700;">消息关联表更新记录</div>
      <a-table :columns="msgLinkTableColumns" :data-source="msgLinkTableData" bordered :scroll="{ y: 300 }">
        <template #bodyCell="{ text, record, index, column }">
          <template v-if="column.dataIndex === 'accuracyMap'">
            <div style="text-align: center;">
              <div style="color:#00B42A">{{ record.accuracyMap }}</div>
            </div>
          </template>
          <template v-if="column.dataIndex === 'action'">
            <div style="text-align: center;color: #3cb0ff;cursor: pointer;">
              需关注
            </div>
          </template>
        </template>
      </a-table>
    </div>
    <div>
      <div style="margin: 10px 0;font-weight: 700;">数据表详细变更</div>
      <a-collapse v-model:activeKey="detailTableChangeHisKey">
        <a-collapse-panel key="1" header="t_production_order">
        </a-collapse-panel>
        <a-collapse-panel key="2" header="t_production_batch（生产批次表）">
        </a-collapse-panel>
        <a-collapse-panel key="3" header="t_product_quality（产品质检表）">
        </a-collapse-panel>
      </a-collapse>
    </div>


    <div>
      <div style="display: flex;align-items: center;justify-content: space-around;">
        <div style="flex: 1 1 calc(50% - 10px);margin: 10px 0;font-weight: 700;">系统A (源系统) - 消息完整数据</div>
        <div style="flex: 1 1 calc(50% - 10px);margin: 10px 0;font-weight: 700;">系统B (目标系统) - 消息同步数据</div>
      </div>
      
      <div style="height: 270px;overflow-y: auto;display: flex;gap: 10px;padding: 20px;background: #ececec;">

        <div style="flex: 1 1 calc(50% - 10px);">
          <div style="box-sizing: content-box;">
            <div style="color: #165dff;"></div>
            <div>
              <a-form :model="sysMsgForm" name="sysMsgForm" class="sysMsgFormClass">
                <a-card title="t_production_order（生产订单表）" :bordered="false">
                  <a-row :gutter="24">
                    <a-col :span="24">
                      <a-form-item label="行ID" name="id">
                        <div style="">{{ sysMsgForm.id }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="order_id" name="order_id">
                        <div style="">{{ sysMsgForm.order_id }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="product_code" name="product_code">
                        <div style="">{{ sysMsgForm.product_code }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="production_qty" name="production_qty">
                        <div style="">{{ sysMsgForm.production_qty }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="production_date" name="production_date">
                        <div style=""> {{ sysMsgForm.production_date }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="production_time" name="production_time">
                        <div style="">{{ sysMsgForm.production_time }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="status" name="status">
                        <div style="">{{ sysMsgForm.status }}</div>
                      </a-form-item>
                    </a-col>
                  </a-row>
                </a-card>

              </a-form>
            </div>
          </div>
        </div>

        <div style="flex: 1 1 calc(50% - 10px);">
          <div style="box-sizing: content-box;">
            <div style="color: #165dff;"></div>
            <div>
              <a-form :model="sysMsgForm" name="sysMsgForm" class="sysMsgFormClass">
                <a-card title="t_production_order（生产订单表）" :bordered="false">
                  <a-row :gutter="24">
                    <a-col :span="24">
                      <a-form-item label="行ID" name="id">
                        <div style="">{{ sysMsgForm.id }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="order_id" name="order_id">
                        <div style="">{{ sysMsgForm.order_id }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="product_code" name="product_code">
                        <div style="">{{ sysMsgForm.product_code }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="production_qty" name="production_qty">
                        <div style="">{{ sysMsgForm.production_qty }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="production_date" name="production_date">
                        <div style=""> {{ sysMsgForm.production_date }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="production_time" name="production_time">
                        <div style="">{{ sysMsgForm.production_time }}</div>
                      </a-form-item>
                    </a-col>
                    <a-col :span="12">
                      <a-form-item label="status" name="status">
                        <div style="">{{ sysMsgForm.status }}</div>
                      </a-form-item>
                    </a-col>
                  </a-row>
                </a-card>

              </a-form>
            </div>
          </div>
        </div>

      </div>

    </div>
    <div>
      <div style="margin: 10px 0;font-weight: 700;">不一致详情汇总（按影响程度排序）</div>
      <div style="display: flex;flex-wrap: wrap;justify-content: space-around;padding: 20px;background: #ececec;gap:10px;">
        <div style="box-sizing: border-box;flex: 1 1 calc(50% - 10px);border-left: 3px solid #ff7d00;border-radius: 5px;">
          <div style="color: #165dff;"></div>
          <div>
            <a-form :model="sysMsgForm" name="sysMsgForm" class="sysMsgFormClass">
              <a-card title="t_production_order（生产订单表）" :bordered="false">
                <a-row :gutter="24">
                  <a-col :span="24">
                    <a-form-item label="行ID" name="id">
                      <div style="">{{ sysMsgForm.id }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="order_id" name="order_id">
                      <div style="">{{ sysMsgForm.order_id }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="product_code" name="product_code">
                      <div style="">{{ sysMsgForm.product_code }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="production_qty" name="production_qty">
                      <div style="">{{ sysMsgForm.production_qty }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="production_date" name="production_date">
                      <div style=""> {{ sysMsgForm.production_date }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="production_time" name="production_time">
                      <div style="">{{ sysMsgForm.production_time }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="status" name="status">
                      <div style="">{{ sysMsgForm.status }}</div>
                    </a-form-item>
                  </a-col>
                </a-row>
              </a-card>

            </a-form>
          </div>
        </div>
        <div style="box-sizing: border-box;flex: 1 1 calc(50% - 10px);">
          <div style="color: #165dff;"></div>
          <div>
            <a-form :model="sysMsgForm" name="sysMsgForm" class="sysMsgFormClass">
              <a-card title="t_production_order（生产订单表）" :bordered="false">
                <a-row :gutter="24">
                  <a-col :span="24">
                    <a-form-item label="行ID" name="id">
                      <div style="">{{ sysMsgForm.id }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="order_id" name="order_id">
                      <div style="">{{ sysMsgForm.order_id }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="product_code" name="product_code">
                      <div style="">{{ sysMsgForm.product_code }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="production_qty" name="production_qty">
                      <div style="">{{ sysMsgForm.production_qty }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="production_date" name="production_date">
                      <div style=""> {{ sysMsgForm.production_date }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="production_time" name="production_time">
                      <div style="">{{ sysMsgForm.production_time }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="status" name="status">
                      <div style="">{{ sysMsgForm.status }}</div>
                    </a-form-item>
                  </a-col>
                </a-row>
              </a-card>

            </a-form>
          </div>
        </div>
        <div style="box-sizing: border-box;flex: 1 1 calc(50% - 10px);">
          <div style="color: #165dff;"></div>
          <div>
            <a-form :model="sysMsgForm" name="sysMsgForm" class="sysMsgFormClass">
              <a-card title="t_production_order（生产订单表）" :bordered="false">
                <a-row :gutter="24">
                  <a-col :span="24">
                    <a-form-item label="行ID" name="id">
                      <div style="">{{ sysMsgForm.id }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="order_id" name="order_id">
                      <div style="">{{ sysMsgForm.order_id }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="product_code" name="product_code">
                      <div style="">{{ sysMsgForm.product_code }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="production_qty" name="production_qty">
                      <div style="">{{ sysMsgForm.production_qty }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="production_date" name="production_date">
                      <div style=""> {{ sysMsgForm.production_date }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="production_time" name="production_time">
                      <div style="">{{ sysMsgForm.production_time }}</div>
                    </a-form-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-item label="status" name="status">
                      <div style="">{{ sysMsgForm.status }}</div>
                    </a-form-item>
                  </a-col>
                </a-row>
              </a-card>

            </a-form>
          </div>
        </div>
      </div>
    </div>
  </a-modal>
</template>
<script setup>
import { nextTick, onMounted, onUnmounted, reactive, ref } from 'vue';
import dayjs from "dayjs";
import { MessageOutlined, ApartmentOutlined, AppstoreOutlined, CarryOutOutlined, } from '@ant-design/icons-vue';
import * as echarts from 'echarts'


let searchFormState = reactive({
  factory: 'all',
  msg: ['all'],
  line: ['all'],
  cst: ['all'],
  time: [],
});


let factoryList = ref([
  { label: 'All Factory', value: 'all' },
  { label: 'D3T', value: 'D3T' },
  { label: 'D3F', value: 'D3F' },
  { label: 'D3C', value: 'D3C' },
])
let msgList = ref([
  { label: 'All Msg', value: 'all' },
  { label: 'ELDC', value: 'ELDC' },
  { label: 'EVNT', value: 'EVNT' },
  { label: 'EMAP', value: 'EMAP' },
  { label: 'CMAP', value: 'CMAP' },
])
let lineList = ref([
  { label: 'All Line', value: 'all' },
  { label: '一号生产线', value: 'line1' },
  { label: '二号生产线', value: 'line2' },
  { label: '三号生产线', value: 'line3' },
])
let cstList = ref([
  { label: 'B20250601001', value: 'B20250601001' },
  { label: 'B20250601002', value: 'B20250601002' },
  { label: 'B20250601003', value: 'B20250601003' },
])

let rangePresets = ref([
  { label: 'Last 7 Days', value: [dayjs().add(-7, 'd'), dayjs()] },
  { label: 'Last 14 Days', value: [dayjs().add(-14, 'd'), dayjs()] },
  { label: 'Last 30 Days', value: [dayjs().add(-30, 'd'), dayjs()] },
  { label: 'Last 90 Days', value: [dayjs().add(-90, 'd'), dayjs()] },
]);
let search = () => {
  console.log(searchFormState);
};



let echartRange = ref('day')
let echartRangeChane = (val) => {
  console.log(val.value, 'echartRangeChane');
}



const chartDom = ref()
let myChart = null

// 趋势图表数据
const trendData = {
  labels: ['00:00', '03:00', '06:00', '09:00', '12:00', '15:00', '18:00', '21:00'],
  messageAccuracy: [100, 100, 100, 100, 100, 100, 100, 100],
  cstAccuracy: [22.2, 38.4, 58.5, 98.6, 38.8, 38.7, 68.9, 98.76],
  lineAccuracy: [15.3, 29.4, 99.5, 89.6, 29.5, 39.4, 49.5, 79.51],
  tableAccuracy: [17.7, 19.8, 79.8, 39.9, 29.9, 49.8, 89.9, 99.87]
};
const initChart = () => {
  myChart = echarts.init(chartDom.value)
  const option = {
    tooltip: {
      trigger: 'axis'
    },
    legend: {
      data: ['消息准确率', 'CST准确率', '线体准确率', '表准确率'],
      top: '0%',
      left: 'center',
      itemWidth: 12,
      itemHeight: 12,
      textStyle: {
        fontSize: 12
      },
      orient: 'horizontal',
      itemGap: 20
    },
    grid: {
      left: '3%',
      right: '4%',
      bottom: '3%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      boundaryGap: false,
      data: trendData.labels
    },
    yAxis: {
      type: 'value',
      axisLabel: {
        formatter: '{value}%'
      },
      min: 0,
      max: 100,
      interval: 10,
      axisTick: {
        show: true
      },
      splitLine: {
        show: true
      }
    },
    series: [
      {
        name: '消息准确率',
        type: 'line',
        smooth: true,
        data: trendData.messageAccuracy
      },
      {
        name: 'CST准确率',
        type: 'line',
        smooth: true,
        data: trendData.cstAccuracy
      },
      {
        name: '线体准确率',
        type: 'line',
        smooth: true,
        data: trendData.lineAccuracy
      },
      {
        name: '表准确率',
        type: 'line',
        smooth: true,
        data: trendData.tableAccuracy
      },
    ]
  };
  //使用配置
  myChart.setOption(option)
}



let accuracyMatrixColumns = [
  {
    title: '消息名',
    dataIndex: 'msgType',
  },
  {
    title: 'D3TPHT',
    dataIndex: '0',
  },
  {
    title: 'D3TICL',
    dataIndex: '1',
  },
  {
    title: 'D3TSTR',
    dataIndex: '2',
  },
  {
    title: 'D3TGLC',
    dataIndex: '3',
  },
  {
    title: 'D3TCVD',
    dataIndex: '4',
  },
  {
    title: 'D3TTHS',
    dataIndex: '5',
  },
]

let accuracyMatrixData = ref([
  {
    msgType: 'ELDC',
    lines: [99.25, 98.76, 99.42, 98.53, 99.17, 98.92],
    counts: [328, 296, 315, 287, 302, 291]
  },
  {
    msgType: 'EMAP',
    lines: [98.53, 97.89, 98.76, 97.45, 98.21, 97.95],
    counts: [276, 253, 268, 241, 259, 248]
  },
  {
    msgType: 'EVNT',
    lines: [97.89, 96.54, 98.12, 96.21, 97.56, 97.13],
    counts: [342, 318, 335, 304, 327, 315]
  },
  {
    msgType: 'ERGI',
    lines: [96.72, 95.98, 97.15, 95.63, 96.42, 96.07],
    counts: [254, 237, 246, 229, 239, 231]
  },
  {
    msgType: 'ECUR',
    lines: [95.36, 94.72, 95.89, 94.35, 95.12, 94.87],
    counts: [198, 185, 193, 179, 188, 182]
  }
])
let chartDom1 = ref()
let myChart1 = null
let metricsDetailTitle = ref('')
let metricsDetailVisibile = ref(false)
let openMetricsDetailForm = reactive({
  msgType: '',
  line: '',
  accuracy: '',
  total: '',
  match: '',
  mismatch: '',
})
let openMetricsDetailModal = (row) => {
  metricsDetailTitle.value = `准确度详情 - ${row.msgType}`
  Object.assign(openMetricsDetailForm, {
    msgType: row.msgType,
    line: '一号生产线',
    accuracy: '99.25%',
    total: '328',
    match: '326',
    mismatch: '2',
  })
  metricsDetailVisibile.value = true
  setTimeout(() => {
    iniMetricsDetailChart(row)
  }, 200)
}
const iniMetricsDetailChart = (row) => {
  myChart1 = echarts.init(chartDom1.value)
  const coreAcc = row.accuracy && (parseFloat(row.accuracy) + 0.25).toFixed(2) || 100;
  const normalAcc = row.accuracy && (parseFloat(row.accuracy) - 0.45).toFixed(2) || 100;
  const option = {
    xAxis: {
      type: 'category',
      data: ['核心栏位', '一般栏位'],
    },
    yAxis: {
      type: 'value',
      axisLabel: {
        formatter: '{value}%'
      },
      min: 0,
      max: 100,
      interval: 10,
      axisTick: {
        show: true
      },
      splitLine: {
        show: true
      }
    },
    series: [
      {
        data: [coreAcc, normalAcc],
        type: 'bar'
      }
    ]
  };
  //使用配置
  myChart1.setOption(option)
}



let comparisonSearchVal = ref('')
let comparisonSearchValChange = (val) => {
  console.log(val.target.value, 'comparisonSearchValChange');
}

let comparisonTableColumns = [
  {
    title: '消息ID',
    dataIndex: 'id',
  },
  {
    title: 'CST号',
    dataIndex: 'cstId',
  },
  {
    title: '时间',
    dataIndex: 'timestamp',
  },
  {
    title: '消息',
    dataIndex: 'msgResult',
  },
  {
    title: '卡匣',
    dataIndex: 'cstResult',
  },
  {
    title: '表',
    dataIndex: 'normalFields',
  },
  {
    title: '相近率',
    dataIndex: 'accuracyMap',
  },
  {
    title: '操作',
    dataIndex: 'action',
  },
]
let comparisonTableData = ref([
  { id: 'M20250601123456', cstId: 'B20250601001', line: '一号生产线', timestamp: '2025-06-01 14:32:45', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123457', cstId: 'B20250601001', line: '一号生产线', timestamp: '2025-06-01 14:35:12', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123458', cstId: 'B20250601002', line: '二号生产线', timestamp: '2025-06-01 14:38:05', msgResult: '一致', cstResult: '不一致', normalFields: '一致', result: '核心栏位不一致', status: 'danger', accuracyMap: '98.00%' },
  { id: 'M20250601123459', cstId: 'B20250601002', line: '二号生产线', timestamp: '2025-06-01 14:40:33', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123460', cstId: 'B20250601003', line: '三号生产线', timestamp: '2025-06-01 14:42:18', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123461', cstId: 'B20250601003', line: '三号生产线', timestamp: '2025-06-01 14:45:02', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123462', cstId: 'B20250601004', line: '一号生产线', timestamp: '2025-06-01 14:47:29', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123463', cstId: 'B20250601004', line: '一号生产线', timestamp: '2025-06-01 14:50:11', msgResult: '一致', cstResult: '不一致', normalFields: '不一致', result: '核心栏位不一致', status: 'danger', accuracyMap: '98.00%' },
  { id: 'M20250601123464', cstId: 'B20250601005', line: '二号生产线', timestamp: '2025-06-01 14:53:45', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123465', cstId: 'B20250601005', line: '二号生产线', timestamp: '2025-06-01 14:56:22', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123466', cstId: 'B20250601006', line: '三号生产线', timestamp: '2025-06-01 14:59:08', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123467', cstId: 'B20250601006', line: '三号生产线', timestamp: '2025-06-01 15:02:33', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123468', cstId: 'B20250601007', line: '一号生产线', timestamp: '2025-06-01 15:05:17', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123469', cstId: 'B20250601007', line: '一号生产线', timestamp: '2025-06-01 15:08:42', msgResult: '一致', cstResult: '不一致', normalFields: '一致', result: '核心栏位不一致', status: 'danger', accuracyMap: '98.00%' },
  { id: 'M20250601123470', cstId: 'B20250601008', line: '二号生产线', timestamp: '2025-06-01 15:11:29', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123471', cstId: 'B20250601008', line: '二号生产线', timestamp: '2025-06-01 15:14:05', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123472', cstId: 'B20250601009', line: '三号生产线', timestamp: '2025-06-01 15:17:33', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123473', cstId: 'B20250601009', line: '三号生产线', timestamp: '2025-06-01 15:20:18', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123474', cstId: 'B20250601010', line: '一号生产线', timestamp: '2025-06-01 15:23:45', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123475', cstId: 'B20250601010', line: '一号生产线', timestamp: '2025-06-01 15:26:22', msgResult: '一致', cstResult: '不一致', normalFields: '不一致', result: '核心栏位不一致', status: 'danger', accuracyMap: '98.00%' },
])

let metricsDetailTableColumns = [
  {
    title: '消息ID',
    dataIndex: 'id',
    width: 180,
  },
  {
    title: '卡匣ID',
    dataIndex: 'cstId',
  },
  {
    title: '设备ID',
    dataIndex: 'timestamp',
    width: 180,
  },
  {
    title: '卡匣正确率',
    dataIndex: 'msgResult',
  },
  {
    title: '消息正确率',
    dataIndex: 'cstResult',
  },
  {
    title: '表正确率',
    dataIndex: 'normalFields',
  },
  {
    title: '操作详情',
    dataIndex: 'action',
  },
]
let metricsDetailTableData = ref([
  { id: 'M20250601123456', cstId: 'B20250601001', line: '一号生产线', timestamp: '2025-06-01 14:32:45', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123457', cstId: 'B20250601001', line: '一号生产线', timestamp: '2025-06-01 14:35:12', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123458', cstId: 'B20250601002', line: '二号生产线', timestamp: '2025-06-01 14:38:05', msgResult: '一致', cstResult: '不一致', normalFields: '一致', result: '核心栏位不一致', status: 'danger', accuracyMap: '98.00%' },
  { id: 'M20250601123459', cstId: 'B20250601002', line: '二号生产线', timestamp: '2025-06-01 14:40:33', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123460', cstId: 'B20250601003', line: '三号生产线', timestamp: '2025-06-01 14:42:18', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123461', cstId: 'B20250601003', line: '三号生产线', timestamp: '2025-06-01 14:45:02', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123462', cstId: 'B20250601004', line: '一号生产线', timestamp: '2025-06-01 14:47:29', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123463', cstId: 'B20250601004', line: '一号生产线', timestamp: '2025-06-01 14:50:11', msgResult: '一致', cstResult: '不一致', normalFields: '不一致', result: '核心栏位不一致', status: 'danger', accuracyMap: '98.00%' },
  { id: 'M20250601123464', cstId: 'B20250601005', line: '二号生产线', timestamp: '2025-06-01 14:53:45', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123465', cstId: 'B20250601005', line: '二号生产线', timestamp: '2025-06-01 14:56:22', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123466', cstId: 'B20250601006', line: '三号生产线', timestamp: '2025-06-01 14:59:08', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123467', cstId: 'B20250601006', line: '三号生产线', timestamp: '2025-06-01 15:02:33', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123468', cstId: 'B20250601007', line: '一号生产线', timestamp: '2025-06-01 15:05:17', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123469', cstId: 'B20250601007', line: '一号生产线', timestamp: '2025-06-01 15:08:42', msgResult: '一致', cstResult: '不一致', normalFields: '一致', result: '核心栏位不一致', status: 'danger', accuracyMap: '98.00%' },
  { id: 'M20250601123470', cstId: 'B20250601008', line: '二号生产线', timestamp: '2025-06-01 15:11:29', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123471', cstId: 'B20250601008', line: '二号生产线', timestamp: '2025-06-01 15:14:05', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123472', cstId: 'B20250601009', line: '三号生产线', timestamp: '2025-06-01 15:17:33', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123473', cstId: 'B20250601009', line: '三号生产线', timestamp: '2025-06-01 15:20:18', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123474', cstId: 'B20250601010', line: '一号生产线', timestamp: '2025-06-01 15:23:45', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123475', cstId: 'B20250601010', line: '一号生产线', timestamp: '2025-06-01 15:26:22', msgResult: '一致', cstResult: '不一致', normalFields: '不一致', result: '核心栏位不一致', status: 'danger', accuracyMap: '98.00%' },
])



let messageDetailVisibile = ref(false);
let messageDetailTitle = ref('')
let openMessageDetailModal = (row) => {
  messageDetailTitle.value = `消息详情 - ${row.id}`
  Object.assign(openMetricsDetailForm, {
    msgType: row.msgType,
    line: '一号生产线',
    accuracy: '99.25%',
    total: '328',
    match: '326',
    mismatch: '2',
  })
  messageDetailVisibile.value = true
}



let msgLinkTableColumns = [
  {
    title: '数据表名',
    dataIndex: 'id',
    width: 180,
  },
  {
    title: '新增行数',
    dataIndex: 'id',
    width: 180,
  },
  {
    title: '删除行数',
    dataIndex: 'id',
    width: 180,
  },
  {
    title: '更新行数',
    dataIndex: 'id',
    width: 180,
  },
  {
    title: '标的更新',
    dataIndex: 'id',
    width: 180,
  },
  {
    title: '非标更新',
    dataIndex: 'id',
    width: 180,
  },
  {
    title: '正确率',
    dataIndex: 'id',
    width: 180,
  },
  {
    title: '',
    dataIndex: 'action',
    width: 180,
  },
]

let msgLinkTableData = ref([
  { id: 'M20250601123456', cstId: 'B20250601001', line: '一号生产线', timestamp: '2025-06-01 14:32:45', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123457', cstId: 'B20250601001', line: '一号生产线', timestamp: '2025-06-01 14:35:12', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123458', cstId: 'B20250601002', line: '二号生产线', timestamp: '2025-06-01 14:38:05', msgResult: '一致', cstResult: '不一致', normalFields: '一致', result: '核心栏位不一致', status: 'danger', accuracyMap: '98.00%' },
  { id: 'M20250601123459', cstId: 'B20250601002', line: '二号生产线', timestamp: '2025-06-01 14:40:33', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123460', cstId: 'B20250601003', line: '三号生产线', timestamp: '2025-06-01 14:42:18', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123461', cstId: 'B20250601003', line: '三号生产线', timestamp: '2025-06-01 14:45:02', msgResult: '一致', cstResult: '一致', normalFields: '不一致', result: '一般栏位不一致', status: 'warning', accuracyMap: '98.00%' },
  { id: 'M20250601123462', cstId: 'B20250601004', line: '一号生产线', timestamp: '2025-06-01 14:47:29', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123463', cstId: 'B20250601004', line: '一号生产线', timestamp: '2025-06-01 14:50:11', msgResult: '一致', cstResult: '不一致', normalFields: '不一致', result: '核心栏位不一致', status: 'danger', accuracyMap: '98.00%' },
  { id: 'M20250601123464', cstId: 'B20250601005', line: '二号生产线', timestamp: '2025-06-01 14:53:45', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
  { id: 'M20250601123465', cstId: 'B20250601005', line: '二号生产线', timestamp: '2025-06-01 14:56:22', msgResult: '一致', cstResult: '一致', normalFields: '一致', result: '一致', status: 'success', accuracyMap: '98.00%' },
])

let detailTableChangeHisKey = ref('1')

let sysMsgForm = reactive({
  id: 'PO20250601001（新增）',
  order_id: 'PO20250601001',
  product_code: 'PROD001',
  production_qty: '1000',
  production_date: ' 2025-06-01',
  production_time: '14:30:00',
  status: '已完成',
})
onMounted(() => {
  initChart()
})
onUnmounted(() => {
  if (myChart) {
    myChart.dispose()
  }
  if (myChart1) {
    myChart1.dispose()
  }
})
</script>
<style scoped>
.sysMsgFormClass .ant-form-item {
  margin: 0;
}
.accuracyMatrixClass :deep(td.ant-table-cell){
  padding: 0;
}
</style>