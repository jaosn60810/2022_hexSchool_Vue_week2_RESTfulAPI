<template>
  <div
    class="modal fade"
    id="productModal"
    tabindex="-1"
    role="dialog"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
    ref="modal"
  >
    <div class="modal-dialog modal-xl" role="document">
      <div class="modal-content border-0">
        <!-- modal-header -->
        <div class="modal-header bg-dark text-white">
          <h5 class="modal-title" id="exampleModalLabel">
            <span v-if="isNew">新增產品</span>
            <span v-else>編輯產品</span>
          </h5>
          <button
            type="button"
            class="btn-close btn-close-white"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>

        <!-- modal-body -->
        <div class="modal-body">
          <div class="row">
            <!-- 產品圖片 -->
            <div class="col-sm-4">
              <div class="mb-3">
                <label for="image" class="form-label">輸入圖片網址</label>
                <input
                  type="text"
                  class="form-control"
                  id="image"
                  v-model="tempProduct.imageUrl"
                  placeholder="請輸入圖片連結"
                />
              </div>
              <div class="mb-3">
                <label for="customFile" class="form-label"
                  >或 上傳圖片
                  <div
                    class="spinner-border"
                    role="status"
                    v-if="status.fileUploading"
                  >
                    <span class="visually-hidden">Loading...</span>
                  </div>
                </label>
                <input
                  type="file"
                  id="customFile"
                  class="form-control"
                  ref="fileInput"
                  @change="uploadFile"
                />
              </div>
              <img class="img-fluid" :src="tempProduct.imageUrl" />
              <!-- 延伸技巧，多圖 -->
              <div class="mt-5" v-if="tempProduct.imagesUrl">
                <div
                  v-for="(image, key) in tempProduct.imagesUrl"
                  class="mb-3"
                  :key="key"
                >
                  <input
                    type="url"
                    class="form-control form-control"
                    v-model="tempProduct.imagesUrl[key]"
                    placeholder="請輸入連結"
                  />
                  <div>
                    <img class="img-fluid" :src="image" />
                  </div>
                  <button
                    type="button"
                    class="btn btn-outline-danger"
                    @click="tempProduct.imagesUrl.splice(key, 1)"
                  >
                    移除
                  </button>
                </div>
                <!-- 兩種情況會有新增圖片按鈕
                1. 副圖的最後一個輸入欄有東西
                2. 沒有副圖
                 -->
                <div
                  v-if="
                    tempProduct.imagesUrl[tempProduct.imagesUrl.length - 1] ||
                    !tempProduct.imagesUrl.length
                  "
                >
                  <button
                    class="btn btn-outline-primary btn-sm d-block w-100"
                    @click="tempProduct.imagesUrl.push('')"
                  >
                    新增圖片
                  </button>
                </div>
              </div>
            </div>

            <!-- 產品內容 -->
            <div class="col-sm-8">
              <div class="mb-3">
                <label for="title" class="form-label">標題</label>
                <input
                  type="text"
                  class="form-control"
                  id="title"
                  v-model="tempProduct.title"
                  placeholder="請輸入標題"
                />
              </div>

              <div class="row gx-2">
                <div class="mb-3 col-md-6">
                  <label for="category" class="form-label">分類</label>
                  <input
                    type="text"
                    class="form-control"
                    id="category"
                    v-model="tempProduct.category"
                    placeholder="請輸入分類"
                  />
                </div>
                <div class="mb-3 col-md-6">
                  <label for="price" class="form-label">單位</label>
                  <input
                    type="text"
                    class="form-control"
                    id="unit"
                    v-model="tempProduct.unit"
                    placeholder="請輸入單位"
                  />
                </div>
              </div>

              <div class="row gx-2">
                <div class="mb-3 col-md-6">
                  <label for="origin_price" class="form-label">原價</label>
                  <input
                    type="number"
                    class="form-control"
                    id="origin_price"
                    min="0"
                    v-model.number="tempProduct.origin_price"
                    placeholder="請輸入原價"
                  />
                </div>
                <div class="mb-3 col-md-6">
                  <label for="price" class="form-label">售價</label>
                  <input
                    type="number"
                    class="form-control"
                    id="price"
                    v-model.number="tempProduct.price"
                    min="0"
                    placeholder="請輸入售價"
                  />
                </div>
              </div>
              <hr />

              <div class="mb-3">
                <label for="description" class="form-label">產品描述</label>
                <textarea
                  type="text"
                  class="form-control"
                  id="description"
                  v-model="tempProduct.description"
                  placeholder="請輸入產品描述"
                ></textarea>
              </div>
              <div class="mb-3">
                <label for="content" class="form-label">說明內容</label>
                <textarea
                  type="text"
                  class="form-control"
                  id="content"
                  v-model="tempProduct.content"
                  placeholder="請輸入產品說明內容"
                ></textarea>
              </div>
              <div class="mb-3">
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="checkbox"
                    v-model="tempProduct.is_enabled"
                    :true-value="1"
                    :false-value="0"
                    id="is_enabled"
                  />
                  <label class="form-check-label" for="is_enabled">
                    是否啟用
                  </label>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button
            type="button"
            class="btn btn-outline-secondary"
            data-bs-dismiss="modal"
          >
            取消
          </button>
          <!-- 新增 -->
          <button
            type="button"
            class="btn btn-primary"
            @click="$emit('add-product', tempProduct)"
            v-if="isNew"
          >
            確認
          </button>
          <!-- 編輯 -->
          <button
            type="button"
            class="btn btn-primary"
            @click="$emit('edit-product', tempProduct)"
            v-else
          >
            確認
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import modalMixin from '@/mixins/modalMixin';

export default {
  props: {
    product: {
      type: Object,
      default() {
        return {};
      },
    },
    isNew: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      status: {
        fileUploading: false,
      },
      tempProduct: {},
    };
  },
  emits: ['add-product', 'edit-product'],
  mixins: [modalMixin],
  watch: {
    product() {
      // 因為單向數據流，不行直接修改 props 資料
      this.tempProduct = this.product;
      // 如果沒有副圖，給一個空陣列，避免 undefined
      if (!this.tempProduct.imagesUrl) {
        this.tempProduct.imagesUrl = [];
      }
      // 如果沒有主圖，給一個空字串，避免 undefined
      if (!this.tempProduct.imageUrl) {
        this.tempProduct.imageUrl = '';
      }
    },
  },
  methods: {
    uploadFile() {
      const uploadedFile = this.$refs.fileInput.files[0];
      const formData = new FormData();
      // 增加欄位到表單
      formData.append('file-to-upload', uploadedFile);

      //開啟等待動畫
      this.status.fileUploading = true;

      const url = `${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/admin/upload`;
      this.$http
        .post(url, formData, {
          headers: {
            'Content-Type': 'multipart/form-data',
          },
        })
        .then((response) => {
          // 成功
          if (response.data.success) {
            this.tempProduct.imageUrl = response.data.imageUrl;
            this.$httpMessageState(response, '圖片上傳');
          }
          // 失敗
          else {
            this.$httpMessageState(response, '圖片上傳');
          }
          // 清空輸入欄
          this.$refs.fileInput.value = '';
        })
        .catch((error) => {
          this.$httpMessageState(error.response, '圖片失敗');
        })
        .finally(() => {
          // 關閉等待動畫
          this.status.fileUploading = false;
        });
    },
  },
};
</script>
