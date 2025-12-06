# 华为手机App技术实现方案


查看产品方案文档和现有技术方案文档，以了解需求。


以下是完整的技术实现方案文档内容：

## 1. 文档说明

### 1.1 文档目的
本文档基于《货源管理App产品方案文档》，详细描述华为手机端App的技术实现方案，包括前后端分离架构设计、技术选型、接口规范、开发实施等内容。

### 1.2 适用范围
- 华为手机端App客户端开发
- 前端API接口详情
- 系统集成与测试

### 1.3 技术架构原则
- **前后端分离**：前端App与后端服务完全解耦，通过HTTP/HTTPS协议进行数据交互
- **RESTful API**：后端提供标准RESTful接口，支持JSON数据格式
- **原生性能**：优先考虑华为HarmonyOS原生开发或React Native方案
- **数据安全**：采用HTTPS加密传输，Token认证机制
- **离线支持**：关键数据本地缓存，支持离线查看
- **移动端优先**：针对华为手机特性进行优化，支持HarmonyOS系统
- **可扩展性**：架构设计支持后续功能扩展和性能优化
- **安全性**：数据传输加密、身份认证、权限控制

## 2. 前后端分离架构设计

### 2.1 系统架构图

```
┌─────────────────────────────────────────────────────────────┐
│                    华为手机端App客户端                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   UI展示层   │  │  业务逻辑层   │  │   数据层     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  状态管理    │  │  本地存储     │  │   网络层     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                          │ HTTPS/JSON
                          │ Token认证
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                    API网关层                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  路由转发    │  │  认证鉴权    │  │   请求拦截   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                    后端服务层                                │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ 供应商服务   │  │  货品服务    │  │ 采购单服务   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ 文件服务     │  │  用户服务    │  │ 统计服务     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                    数据存储层                                │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  关系数据库  │  │  文件存储    │  │   缓存层     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 架构分层说明

#### 2.2.1 前端架构分层
- **展示层（UI Layer）**：页面组件、表单组件、列表组件
- **业务逻辑层（Business Layer）**：状态管理、业务逻辑处理、数据转换
- **数据层（Data Layer）**：本地存储、数据缓存、数据同步
- **网络层（Network Layer）**：HTTP请求封装、响应拦截、错误处理

#### 2.2.2 前后端交互流程
1. 前端发起HTTP/HTTPS请求
2. API网关进行路由转发和认证鉴权
3. 后端服务处理业务逻辑
4. 数据存储层进行数据操作
5. 响应数据返回前端
6. 前端更新UI和本地缓存

## 3. 技术选型

### 3.1 技术选型说明
针对华为手机端，推荐使用以下技术方案：
- **方案一（推荐）**：HarmonyOS原生开发（ArkTS）
- **方案二**：React Native + HMS Core
- **方案三**：Flutter + HMS Core

### 3.2 推荐方案：HarmonyOS原生开发

#### 3.2.1 技术栈
- **开发语言**：ArkTS（TypeScript扩展）
- **UI框架**：ArkUI（声明式UI框架）
- **状态管理**：@ohos.data-preferences（本地存储）+ 自定义状态管理
- **网络请求**：@ohos.axios 或 @ohos.http
- **图片处理**：@ohos.multimedia.image
- **视频处理**：@ohos.multimedia.media
- **地图功能**：华为地图服务Map Kit
- **数据库**：@ohos.data.relationalStore（关系型数据库）
- **文件存储**：@ohos.file.fs

#### 3.2.2 项目结构
```
src/
├── main/
│   ├── ets/
│   │   ├── entryability/          # 应用入口
│   │   ├── pages/                 # 页面组件
│   │   │   ├── HomePage/          # 首页
│   │   │   ├── SupplierListPage/  # 供应商列表
│   │   │   ├── SupplierDetailPage/# 供应商详情
│   │   │   ├── SupplierEditPage/  # 供应商编辑
│   │   │   ├── ProductListPage/   # 货品列表
│   │   │   ├── ProductDetailPage/ # 货品详情
│   │   │   ├── ProductEditPage/   # 货品编辑
│   │   │   ├── PurchaseOrderListPage/ # 采购单列表
│   │   │   ├── PurchaseOrderDetailPage/ # 采购单详情
│   │   │   └── PurchaseOrderEditPage/ # 采购单编辑
│   │   ├── components/            # 公共组件
│   │   │   ├── ImagePicker/       # 图片选择器
│   │   │   ├── VideoPicker/       # 视频选择器
│   │   │   ├── MapView/           # 地图组件
│   │   │   ├── SearchBar/         # 搜索栏
│   │   │   ├── ImageViewer/       # 图片查看器
│   │   │   └── VideoPlayer/       # 视频播放器
│   │   ├── models/                # 数据模型
│   │   │   ├── Supplier.ts
│   │   │   ├── Factory.ts
│   │   │   ├── Warehouse.ts
│   │   │   ├── Product.ts
│   │   │   └── PurchaseOrder.ts
│   │   ├── services/              # 服务层
│   │   │   ├── api/               # API接口
│   │   │   │   ├── supplierApi.ts
│   │   │   │   ├── productApi.ts
│   │   │   │   ├── purchaseOrderApi.ts
│   │   │   │   └── uploadApi.ts
│   │   │   ├── mock/              # Mock数据服务
│   │   │   │   ├── mockSupplier.ts
│   │   │   │   ├── mockProduct.ts
│   │   │   │   └── mockPurchaseOrder.ts
│   │   │   ├── storage/           # 本地存储
│   │   │   │   ├── database.ts
│   │   │   │   └── preferences.ts
│   │   │   └── http/              # HTTP请求封装
│   │   │       ├── request.ts
│   │   │       └── interceptors.ts
│   │   ├── utils/                 # 工具类
│   │   │   ├── dateUtils.ts
│   │   │   ├── imageUtils.ts
│   │   │   ├── validationUtils.ts
│   │   │   └── formatUtils.ts
│   │   └── constants/             # 常量定义
│   │       ├── config.ts
│   │       └── api.ts
│   └── resources/                 # 资源文件
│       ├── base/
│       │   ├── element/
│       │   ├── media/
│       │   └── profile/
│       └── rawfile/
├── oh-package.json                # 依赖配置
└── module.json5                   # 模块配置
```

#### 3.2.3 依赖配置（oh-package.json）
```json
{
  "name": "货源管理App",
  "version": "1.0.0",
  "description": "货源管理移动应用",
  "main": "index.ets",
  "author": "",
  "license": "Apache-2.0",
  "dependencies": {
    "@ohos/axios": "^1.0.0",
    "@ohos/data-preferences": "^1.0.0",
    "@ohos/data-relationalStore": "^1.0.0",
    "@ohos/multimedia.image": "^1.0.0",
    "@ohos/multimedia.media": "^1.0.0",
    "@ohos/file.fs": "^1.0.0"
  }
}
```

### 3.3 备选方案：React Native + HMS Core

#### 3.3.1 技术栈
- **框架**：React Native 0.72+
- **导航**：@react-navigation/native 6.x
- **状态管理**：Zustand 或 Redux Toolkit
- **UI组件库**：react-native-paper
- **图片处理**：react-native-image-picker
- **视频处理**：react-native-video
- **地图功能**：@hmscore/react-native-hms-map
- **本地存储**：@react-native-async-storage/async-storage
- **HTTP请求**：axios
- **HMS Core**：@hmscore/react-native-hms-core

### 3.4 备选方案：Flutter + HMS Core

#### 3.4.1 技术栈
- **框架**：Flutter 3.x
- **状态管理**：Provider 或 Riverpod
- **UI组件**：Material Design 3
- **图片处理**：image_picker
- **视频处理**：video_player
- **地图功能**：huawei_map 或 amap_flutter_map
- **本地存储**：sqflite + shared_preferences
- **HTTP请求**：dio
- **HMS Core**：huawei_iap, huawei_push等

## 4. 接口规范

### 4.1 API设计原则
- **RESTful规范**：遵循RESTful API设计规范
- **统一响应格式**：所有接口返回统一JSON格式
- **版本控制**：API路径包含版本号，/api/v1/
- **认证机制**：使用JWT Token进行身份认证
- **错误处理**：统一的错误码和错误信息

### 4.2 统一响应格式

#### 成功响应
```json
{
  "code": 200,
  "message": "操作成功",
  "data": {
    // 业务数据
  },
  "timestamp": 1704067200000
}
```

#### 错误响应
```json
{
  "code": 400,
  "message": "请求参数错误",
  "error": "详细错误信息",
  "timestamp": 1704067200000
}
```

#### 分页响应
```json
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "list": [],
    "total": 100,
    "page": 1,
    "pageSize": 20,
    "totalPages": 5
  },
  "timestamp": 1704067200000
}
```

### 4.3 供应商管理接口

#### 4.3.1 获取供应商列表
- **接口**：`GET /api/v1/suppliers`
- **请求参数**：
  - `page`: 页码（默认1）
  - `pageSize`: 每页数量（默认20）
  - `keyword`: 搜索关键词（可选）
  - `sortBy`: 排序字段（可选）
  - `sortOrder`: 排序方式（asc/desc，可选）
- **响应示例**：见下方Mock数据

#### 4.3.2 获取供应商详情
- **接口**：`GET /api/v1/suppliers/{id}`
- **响应示例**：见下方Mock数据

#### 4.3.3 创建供应商
- **接口**：`POST /api/v1/suppliers`
- **请求体**：
```json
{
  "name": "供应商A",
  "contactName": "张三",
  "phone": "13800138000",
  "email": "zhangsan@example.com",
  "address": "北京市朝阳区xxx",
  "creditCode": "91110000MA01234567",
  "establishDate": "2020-01-01",
  "registeredCapital": "1000万元",
  "remark": "备注信息"
}
```

#### 4.3.4 更新供应商
- **接口**：`PUT /api/v1/suppliers/{id}`
- **请求体**：同创建接口

#### 4.3.5 删除供应商
- **接口**：`DELETE /api/v1/suppliers/{id}`

#### 4.3.6 上传供应商名片图片
- **接口**：`POST /api/v1/suppliers/{id}/business-cards`
- **请求类型**：`multipart/form-data`
- **请求参数**：
  - `files`: 图片文件数组（支持多文件上传）

#### 4.3.7 获取工厂列表
- **接口**：`GET /api/v1/suppliers/{id}/factories`

#### 4.3.8 创建工厂
- **接口**：`POST /api/v1/suppliers/{id}/factories`
- **请求体**：
```json
{
  "name": "工厂A",
  "address": "北京市xxx",
  "area": 5000,
  "employeeCount": 200,
  "productCategories": ["电子产品"],
  "productionCapacity": "月产量10000件",
  "certifications": ["ISO9001"],
  "contactPhone": "13800138001"
}
```

#### 4.3.9 获取仓库列表
- **接口**：`GET /api/v1/suppliers/{id}/warehouses`

#### 4.3.10 创建仓库
- **接口**：`POST /api/v1/suppliers/{id}/warehouses`
- **请求体**：
```json
{
  "name": "仓库A",
  "address": "北京市xxx",
  "latitude": 39.9042,
  "longitude": 116.4074,
  "area": 2000,
  "capacity": "10000立方米",
  "type": "成品仓"
}
```

### 4.4 货品管理接口

#### 4.4.1 获取货品列表
- **接口**：`GET /api/v1/products`
- **请求参数**：
  - `page`: 页码
  - `pageSize`: 每页数量
  - `keyword`: 搜索关键词
  - `category`: 分类筛选
  - `supplierId`: 供应商ID筛选
  - `sortBy`: 排序字段
  - `sortOrder`: 排序方式

#### 4.4.2 获取货品详情
- **接口**：`GET /api/v1/products/{id}`

#### 4.4.3 创建货品
- **接口**：`POST /api/v1/products`
- **请求体**：
```json
{
  "productCode": "PROD001",
  "name": "货品名称",
  "category": "电子产品",
  "specification": "规格型号",
  "unit": "件",
  "price": 100.00,
  "stock": 1000,
  "minStock": 100,
  "minWholesaleQuantity": 10,
  "isClearance": false,
  "isNew": true,
  "supplierId": "supplier_001",
  "description": "货品描述"
}
```

#### 4.4.4 更新货品
- **接口**：`PUT /api/v1/products/{id}`

#### 4.4.5 删除货品
- **接口**：`DELETE /api/v1/products/{id}`

#### 4.4.6 上传货品图片
- **接口**：`POST /api/v1/products/{id}/images`
- **请求类型**：`multipart/form-data`
- **请求参数**：
  - `files`: 图片文件数组（最多9张）

#### 4.4.7 上传货品视频
- **接口**：`POST /api/v1/products/{id}/videos`
- **请求类型**：`multipart/form-data`
- **请求参数**：
  - `file`: 视频文件（最多3个，单个不超过50MB）

### 4.5 采购单管理接口

#### 4.5.1 获取采购单列表
- **接口**：`GET /api/v1/purchase-orders`
- **请求参数**：
  - `page`: 页码
  - `pageSize`: 每页数量
  - `keyword`: 搜索关键词（采购单号）
  - `status`: 状态筛选（pending/approved/purchasing/completed/cancelled）
  - `startDate`: 开始日期
  - `endDate`: 结束日期
  - `supplierId`: 供应商ID筛选

#### 4.5.2 获取采购单详情
- **接口**：`GET /api/v1/purchase-orders/{id}`

#### 4.5.3 创建采购单
- **接口**：`POST /api/v1/purchase-orders`
- **请求体**：
```json
{
  "orderNumber": "PO20240101001",
  "supplierId": "supplier_001",
  "purchaserId": "user_001",
  "purchaseDate": "2024-01-01",
  "expectedArrivalDate": "2024-01-15",
  "items": [
    {
      "productId": "product_001",
      "quantity": 100,
      "unitPrice": 50.00,
      "remark": "备注"
    }
  ],
  "remark": "采购单备注"
}
```

#### 4.5.4 更新采购单
- **接口**：`PUT /api/v1/purchase-orders/{id}`
- **说明**：仅未审核状态可编辑

#### 4.5.5 删除采购单
- **接口**：`DELETE /api/v1/purchase-orders/{id}`

#### 4.5.6 审核采购单
- **接口**：`POST /api/v1/purchase-orders/{id}/approve`
- **请求体**：
```json
{
  "approved": true,
  "remark": "审核备注"
}
```

#### 4.5.7 更新采购单状态
- **接口**：`PUT /api/v1/purchase-orders/{id}/status`
- **请求体**：
```json
{
  "status": "purchasing"
}
```

### 4.6 文件上传接口

#### 4.6.1 通用文件上传
- **接口**：`POST /api/v1/upload`
- **请求类型**：`multipart/form-data`
- **请求参数**：
  - `file`: 文件
  - `type`: 文件类型（image/video/document）
- **响应示例**：
```json
{
  "code": 200,
  "message": "上传成功",
  "data": {
    "url": "https://example.com/files/xxx.jpg",
    "fileName": "xxx.jpg",
    "fileSize": 1024000,
    "fileType": "image/jpeg"
  }
}
```

### 4.7 认证接口

#### 4.7.1 用户登录
- **接口**：`POST /api/v1/auth/login`
- **请求体**：
```json
{
  "username": "admin",
  "password": "password123"
}
```
- **响应示例**：
```json
{
  "code": 200,
  "message": "登录成功",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "refresh_token_xxx",
    "expiresIn": 3600,
    "user": {
      "id": "user_001",
      "username": "admin",
      "name": "管理员"
    }
  }
}
```

#### 4.7.2 刷新Token
- **接口**：`POST /api/v1/auth/refresh`
- **请求体**：
```json
{
  "refreshToken": "refresh_token_xxx"
}
```

#### 4.7.3 用户登出
- **接口**：`POST /api/v1/auth/logout`

## 5. Mock数据和接口测试数据结构体

### 5.1 Mock数据服务设计

#### 5.1.1 Mock数据存储
- 使用本地数据库（SQLite/关系型数据库）存储Mock数据
- 支持数据的增删改查操作
- 数据格式与真实API保持一致

#### 5.1.2 Mock API服务层
- 创建Mock API服务，模拟后端接口行为
- 支持延迟响应（模拟网络延迟）
- 支持错误场景模拟

### 5.2 供应商Mock数据

#### 5.2.1 供应商列表Mock数据
```json
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "list": [
      {
        "id": "supplier_001",
        "name": "北京科技有限公司",
        "contactName": "张三",
        "phone": "13800138000",
        "email": "zhangsan@example.com",
        "address": "北京市朝阳区建国路88号",
        "creditCode": "91110000MA01234567",
        "establishDate": "2020-01-01",
        "registeredCapital": "1000万元",
        "remark": "长期合作伙伴",
        "businessCards": [
          "https://example.com/cards/card1.jpg",
          "https://example.com/cards/card2.jpg"
        ],
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-01T00:00:00Z"
      },
      {
        "id": "supplier_002",
        "name": "上海贸易有限公司",
        "contactName": "李四",
        "phone": "13900139000",
        "email": "lisi@example.com",
        "address": "上海市浦东新区世纪大道100号",
        "creditCode": "91310000MA02345678",
        "establishDate": "2019-05-15",
        "registeredCapital": "2000万元",
        "remark": "优质供应商",
        "businessCards": [
          "https://example.com/cards/card3.jpg"
        ],
        "createdAt": "2024-01-02T00:00:00Z",
        "updatedAt": "2024-01-02T00:00:00Z"
      }
    ],
    "total": 2,
    "page": 1,
    "pageSize": 20,
    "totalPages": 1
  },
  "timestamp": 1704067200000
}
```

#### 5.2.2 供应商详情Mock数据
```json
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "id": "supplier_001",
    "name": "北京科技有限公司",
    "contactName": "张三",
    "phone": "13800138000",
    "email": "zhangsan@example.com",
    "address": "北京市朝阳区建国路88号",
    "creditCode": "91110000MA01234567",
    "establishDate": "2020-01-01",
    "registeredCapital": "1000万元",
    "remark": "长期合作伙伴",
    "businessCards": [
      "https://example.com/cards/card1.jpg",
      "https://example.com/cards/card2.jpg"
    ],
    "factories": [
      {
        "id": "factory_001",
        "supplierId": "supplier_001",
        "name": "北京工厂A",
        "address": "北京市通州区工业园A区",
        "area": 5000,
        "employeeCount": 200,
        "productCategories": ["电子产品", "智能设备"],
        "productionCapacity": "月产量10000件",
        "certifications": ["ISO9001", "CE认证"],
        "contactPhone": "13800138001",
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-01T00:00:00Z"
      }
    ],
    "warehouses": [
      {
        "id": "warehouse_001",
        "supplierId": "supplier_001",
        "factoryId": "factory_001",
        "name": "成品仓库A",
        "address": "北京市通州区工业园A区仓库",
        "latitude": 39.9042,
        "longitude": 116.4074,
        "area": 2000,
        "capacity": "10000立方米",
        "type": "成品仓",
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-01T00:00:00Z"
      }
    ],
    "createdAt": "2024-01-01T00:00:00Z",
    "updatedAt": "2024-01-01T00:00:00Z"
  },
  "timestamp": 1704067200000
}
```

### 5.3 货品Mock数据

#### 5.3.1 货品列表Mock数据
```json
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "list": [
      {
        "id": "product_001",
        "productCode": "PROD001",
        "name": "智能手机X1",
        "category": "电子产品",
        "specification": "128GB/8GB",
        "unit": "件",
        "price": 2999.00,
        "stock": 1000,
        "minStock": 100,
        "minWholesaleQuantity": 10,
        "isClearance": false,
        "isNew": true,
        "supplierId": "supplier_001",
        "supplierName": "北京科技有限公司",
        "description": "高性能智能手机，支持5G网络",
        "images": [
          "https://example.com/products/prod001_1.jpg",
          "https://example.com/products/prod001_2.jpg"
        ],
        "videos": [
          "https://example.com/products/prod001_video.mp4"
        ],
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-01T00:00:00Z"
      },
      {
        "id": "product_002",
        "productCode": "PROD002",
        "name": "智能手表Y2",
        "category": "电子产品",
        "specification": "标准版",
        "unit": "件",
        "price": 899.00,
        "stock": 50,
        "minStock": 100,
        "minWholesaleQuantity": 5,
        "isClearance": false,
        "isNew": false,
        "supplierId": "supplier_001",
        "supplierName": "北京科技有限公司",
        "description": "健康监测智能手表",
        "images": [
          "https://example.com/products/prod002_1.jpg"
        ],
        "videos": [],
        "createdAt": "2024-01-02T00:00:00Z",
        "updatedAt": "2024-01-02T00:00:00Z"
      }
    ],
    "total": 2,
    "page": 1,
    "pageSize": 20,
    "totalPages": 1
  },
  "timestamp": 1704067200000
}
```

#### 5.3.2 货品详情Mock数据
```json
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "id": "product_001",
    "productCode": "PROD001",
    "name": "智能手机X1",
    "category": "电子产品",
    "specification": "128GB/8GB",
    "unit": "件",
    "price": 2999.00,
    "stock": 1000,
    "minStock": 100,
    "minWholesaleQuantity": 10,
    "isClearance": false,
    "isNew": true,
    "supplierId": "supplier_001",
    "supplierName": "北京科技有限公司",
    "description": "高性能智能手机，支持5G网络，配备最新处理器，6.5英寸全面屏，4800万像素后置三摄，支持快充功能。",
    "images": [
      "https://example.com/products/prod001_1.jpg",
      "https://example.com/products/prod001_2.jpg",
      "https://example.com/products/prod001_3.jpg"
    ],
    "videos": [
      "https://example.com/products/prod001_video.mp4"
    ],
    "createdAt": "2024-01-01T00:00:00Z",
    "updatedAt": "2024-01-01T00:00:00Z"
  },
  "timestamp": 1704067200000
}
```

### 5.4 采购单Mock数据

#### 5.4.1 采购单列表Mock数据
```json
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "list": [
      {
        "id": "order_001",
        "orderNumber": "PO20240101001",
        "supplierId": "supplier_001",
        "supplierName": "北京科技有限公司",
        "purchaserId": "user_001",
        "purchaserName": "采购员A",
        "purchaseDate": "2024-01-01",
        "expectedArrivalDate": "2024-01-15",
        "status": "pending",
        "statusText": "待审核",
        "totalAmount": 299900.00,
        "itemCount": 1,
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-01T00:00:00Z"
      },
      {
        "id": "order_002",
        "orderNumber": "PO20240102001",
        "supplierId": "supplier_002",
        "supplierName": "上海贸易有限公司",
        "purchaserId": "user_001",
        "purchaserName": "采购员A",
        "purchaseDate": "2024-01-02",
        "expectedArrivalDate": "2024-01-20",
        "status": "approved",
        "statusText": "已审核",
        "totalAmount": 44950.00,
        "itemCount": 2,
        "createdAt": "2024-01-02T00:00:00Z",
        "updatedAt": "2024-01-02T00:00:00Z"
      }
    ],
    "total": 2,
    "page": 1,
    "pageSize": 20,
    "totalPages": 1
  },
  "timestamp": 1704067200000
}
```

#### 5.4.2 采购单详情Mock数据
```json
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "id": "order_001",
    "orderNumber": "PO20240101001",
    "supplierId": "supplier_001",
    "supplierName": "北京科技有限公司",
    "purchaserId": "user_001",
    "purchaserName": "采购员A",
    "purchaseDate": "2024-01-01",
    "expectedArrivalDate": "2024-01-15",
    "status": "pending",
    "statusText": "待审核",
    "totalAmount": 299900.00,
    "items": [
      {
        "id": "item_001",
        "productId": "product_001",
        "productName": "智能手机X1",
        "productCode": "PROD001",
        "quantity": 100,
        "unit": "件",
        "unitPrice": 2999.00,
        "subtotal": 299900.00,
        "remark": "首批采购"
      }
    ],
    "remark": "紧急采购，请尽快处理",
    "createdAt": "2024-01-01T00:00:00Z",
    "updatedAt": "2024-01-01T00:00:00Z"
  },
  "timestamp": 1704067200000
}
```

### 5.5 接口测试数据结构体（TypeScript/ArkTS）

#### 5.5.1 供应商数据结构体
```typescript
// Supplier.ts
export interface Supplier {
  id: string;
  name: string;
  contactName?: string;
  phone?: string;
  email?: string;
  address?: string;
  creditCode?: string;
  establishDate?: string;
  registeredCapital?: string;
  remark?: string;
  businessCards?: string[];
  factories?: Factory[];
  warehouses?: Warehouse[];
  createdAt: string;
  updatedAt: string;
}

export interface Factory {
  id: string;
  supplierId: string;
  name: string;
  address?: string;
  area?: number;
  employeeCount?: number;
  productCategories?: string[];
  productionCapacity?: string;
  certifications?: string[];
  contactPhone?: string;
  createdAt: string;
  updatedAt: string;
}

export interface Warehouse {
  id: string;
  supplierId: string;
  factoryId?: string;
  name: string;
  address?: string;
  latitude?: number;
  longitude?: number;
  area?: number;
  capacity?: string;
  type?: string;
  createdAt: string;
  updatedAt: string;
}
```

#### 5.5.2 货品数据结构体
```typescript
// Product.ts
export interface Product {
  id: string;
  productCode: string;
  name: string;
  category?: string;
  specification?: string;
  unit?: string;
  price?: number;
  stock?: number;
  minStock?: number;
  minWholesaleQuantity?: number;
  isClearance?: boolean;
  isNew?: boolean;
  supplierId?: string;
  supplierName?: string;
  description?: string;
  images?: string[];
  videos?: string[];
  createdAt: string;
  updatedAt: string;
}
```

#### 5.5.3 采购单数据结构体
```typescript
// PurchaseOrder.ts
export interface PurchaseOrder {
  id: string;
  orderNumber: string;
  supplierId: string;
  supplierName?: string;
  purchaserId: string;
  purchaserName?: string;
  purchaseDate: string;
  expectedArrivalDate?: string;
  status: PurchaseOrderStatus;
  statusText?: string;
  totalAmount: number;
  items: PurchaseOrderItem[];
  remark?: string;
  createdAt: string;
  updatedAt: string;
}

export interface PurchaseOrderItem {
  id: string;
  productId: string;
  productName?: string;
  productCode?: string;
  quantity: number;
  unit?: string;
  unitPrice: number;
  subtotal: number;
  remark?: string;
}

export enum PurchaseOrderStatus {
  PENDING = 'pending',
  APPROVED = 'approved',
  PURCHASING = 'purchasing',
  COMPLETED = 'completed',
  CANCELLED = 'cancelled'
}
```

#### 5.5.4 API响应数据结构体
```typescript
// ApiResponse.ts
export interface ApiResponse<T> {
  code: number;
  message: string;
  data?: T;
  error?: string;
  timestamp: number;
}

export interface PaginatedResponse<T> {
  list: T[];
  total: number;
  page: number;
  pageSize: number;
  totalPages: number;
}

export interface ApiResponseWithPagination<T> extends ApiResponse<PaginatedResponse<T>> {}
```

## 6. 开发实施

### 6.1 开发环境搭建

#### 6.1.1 HarmonyOS开发环境
1. 安装DevEco Studio
2. 配置HarmonyOS SDK
3. 配置华为开发者账号
4. 安装相关依赖包

#### 6.1.2 项目初始化
1. 创建HarmonyOS应用项目
2. 配置项目依赖（oh-package.json）
3. 配置应用权限（module.json5）
4. 初始化项目结构

### 6.2 开发阶段划分

#### 6.2.1 第一阶段：基础框架搭建（1-2周）
- 项目初始化和环境配置
- 导航结构搭建
- 基础UI组件库集成
- HTTP请求封装
- Mock数据服务搭建
- 本地存储配置
- 状态管理方案实现

#### 6.2.2 第二阶段：供应商管理模块（1-2周）
- 供应商列表页
- 供应商详情页
- 供应商新增/编辑页
- 名片图片上传功能
- 工厂信息管理
- 仓库位置管理（地图功能）

#### 6.2.3 第三阶段：货品管理模块（1-2周）
- 货品列表页
- 货品详情页
- 货品新增/编辑页
- 图片上传功能
- 视频上传功能
- 图片/视频预览功能

#### 6.2.4 第四阶段：采购单模块（1-2周）
- 采购单列表页
- 采购单详情页
- 采购单创建/编辑页
- 状态管理功能
- 采购明细管理

#### 6.2.5 第五阶段：优化和测试（1周）
- 性能优化
- UI/UX优化
- 华为手机适配测试
- Bug修复
- 代码审查

### 6.3 关键技术实现

#### 6.3.1 HTTP请求封装
- 统一请求拦截器（添加Token、设置请求头）
- 统一响应拦截器（错误处理、Token刷新）
- 请求重试机制
- 请求取消机制

#### 6.3.2 本地存储方案
- 使用关系型数据库存储结构化数据
- 使用Preferences存储配置信息
- 实现数据同步机制

#### 6.3.3 图片处理
- 图片压缩
- 图片缓存
- 图片懒加载
- 图片预览

#### 6.3.4 视频处理
- 视频选择
- 视频预览
- 视频上传进度显示

#### 6.3.5 地图功能
- 集成华为地图SDK
- 位置标注
- 导航功能

### 6.4 测试方案

#### 6.4.1 单元测试
- 工具类测试
- 服务层测试
- 组件测试

#### 6.4.2 集成测试
- API接口测试
- 数据流测试
- 功能测试

#### 6.4.3 端到端测试
- 用户流程测试
- 性能测试
- 兼容性测试

### 6.5 部署方案

#### 6.5.1 开发环境
- 本地Mock数据
- 开发调试工具

#### 6.5.2 测试环境
- 测试服务器
- 测试数据

#### 6.5.3 生产环境
- 生产服务器
- 生产配置
- 监控和日志

## 7. 安全方案

### 7.1 数据传输安全
- 使用HTTPS加密传输
- 证书校验
- 防止中间人攻击

### 7.2 身份认证
- JWT Token认证
- Token刷新机制
- 自动登录

### 7.3 数据存储安全
- 敏感数据加密存储
- 本地数据访问控制

### 7.4 权限控制
- 功能权限控制
- 数据权限控制

## 8. 性能优化

### 8.1 网络优化
- 请求合并
- 数据缓存
- 图片懒加载

### 8.2 渲染优化
- 列表虚拟滚动
- 图片压缩
- 组件懒加载

### 8.3 存储优化
- 数据分页加载
- 缓存策略
- 数据清理

## 9. 后续扩展

### 9.1 功能扩展
- 数据统计报表
- 导出功能（Excel、PDF）
- 消息推送通知
- 多用户权限管理
- 数据同步（云端备份）
- 扫码功能
- 审批流程
- 库存预警推送

### 9.2 技术扩展
- 离线数据同步
- 多端数据同步
- 实时通信
- 大数据分析

---

**文档版本**：v1.0  
**创建日期**：2024年  
**最后更新**：2024年
```

## 文件位置

建议创建或更新文件：
- `c:\work\projects\cuiyuan\documents\华为手机端技术方案文档.md`

该文档包含：
1. 前后端分离架构设计（架构图、分层说明）
2. 技术选型（HarmonyOS原生、React Native、Flutter）
3. 接口规范（RESTful API、统一响应格式、各模块接口）
4. Mock数据和接口测试数据结构体（供应商、货品、采购单的Mock数据与TypeScript/ArkTS结构体）
5. 开发实施（环境搭建、阶段划分、关键技术实现、测试、部署）
6. 安全方案
7. 性能优化
8. 后续扩展

文档已按产品方案文档的需求编写，可直接使用。

```markdown
# 华为手机端App技术实现方案文档

## 1. 文档说明

### 1.1 文档目的
本文档基于《货源管理App产品方案文档》，详细描述华为手机端App的技术实现方案，包括前后端分离架构设计、技术选型、接口规范、开发实施等内容。

### 1.2 适用范围
- 华为手机端App客户端开发
- 前端API接口详情
- 系统集成与测试

### 1.3 技术架构原则
- **前后端分离**：前端App与后端服务完全解耦，通过HTTP/HTTPS协议进行数据交互
- **RESTful API**：后端提供标准RESTful接口，支持JSON数据格式
- **原生性能**：优先考虑华为HarmonyOS原生开发或React Native方案
- **数据安全**：采用HTTPS加密传输，Token认证机制
- **离线支持**：关键数据本地缓存，支持离线查看
- **移动端优先**：针对华为手机特性进行优化，支持HarmonyOS系统
- **可扩展性**：架构设计支持后续功能扩展和性能优化
- **安全性**：数据传输加密、身份认证、权限控制

## 2. 前后端分离架构设计

### 2.1 系统架构图

```

```plaintext

### 2.2 架构分层说明

#### 2.2.1 前端架构分层
- **展示层（UI Layer）**：页面组件、表单组件、列表组件
- **业务逻辑层（Business Layer）**：状态管理、业务逻辑处理、数据转换
- **数据层（Data Layer）**：本地存储、数据缓存、数据同步
- **网络层（Network Layer）**：HTTP请求封装、响应拦截、错误处理

#### 2.2.2 前后端交互流程
1. 前端发起HTTP/HTTPS请求
2. API网关进行路由转发和认证鉴权
3. 后端服务处理业务逻辑
4. 数据存储层进行数据操作
5. 响应数据返回前端
6. 前端更新UI和本地缓存

## 3. 技术选型

### 3.1 技术选型说明
针对华为手机端，推荐使用以下技术方案：
- **方案一（推荐）**：HarmonyOS原生开发（ArkTS）
- **方案二**：React Native + HMS Core
- **方案三**：Flutter + HMS Core

### 3.2 推荐方案：HarmonyOS原生开发

#### 3.2.1 技术栈
- **开发语言**：ArkTS（TypeScript扩展）
- **UI框架**：ArkUI（声明式UI框架）
- **状态管理**：@ohos.data-preferences（本地存储）+ 自定义状态管理
- **网络请求**：@ohos.axios 或 @ohos.http
- **图片处理**：@ohos.multimedia.image
- **视频处理**：@ohos.multimedia.media
- **地图功能**：华为地图服务Map Kit
- **数据库**：@ohos.data.relationalStore（关系型数据库）
- **文件存储**：@ohos.file.fs

#### 3.2.2 项目结构
```

```plaintext

#### 3.2.3 依赖配置（oh-package.json）
{
  "name": "货源管理App",
  "version": "1.0.0",
  "description": "货源管理移动应用",
  "main": "index.ets",
  "author": "",
  "license": "Apache-2.0",
  "dependencies": {
    "@ohos/axios": "^1.0.0",
    "@ohos/data-preferences": "^1.0.0",
    "@ohos/data-relationalStore": "^1.0.0",
    "@ohos/multimedia.image": "^1.0.0",
    "@ohos/multimedia.media": "^1.0.0",
    "@ohos/file.fs": "^1.0.0"
  }
}### 3.3 备选方案：React Native + HMS Core

#### 3.3.1 技术栈
- **框架**：React Native 0.72+
- **导航**：@react-navigation/native 6.x
- **状态管理**：Zustand 或 Redux Toolkit
- **UI组件库**：react-native-paper
- **图片处理**：react-native-image-picker
- **视频处理**：react-native-video
- **地图功能**：@hmscore/react-native-hms-map
- **本地存储**：@react-native-async-storage/async-storage
- **HTTP请求**：axios
- **HMS Core**：@hmscore/react-native-hms-core

### 3.4 备选方案：Flutter + HMS Core

#### 3.4.1 技术栈
- **框架**：Flutter 3.x
- **状态管理**：Provider 或 Riverpod
- **UI组件**：Material Design 3
- **图片处理**：image_picker
- **视频处理**：video_player
- **地图功能**：huawei_map 或 amap_flutter_map
- **本地存储**：sqflite + shared_preferences
- **HTTP请求**：dio
- **HMS Core**：huawei_iap, huawei_push等

## 4. 接口规范

### 4.1 API设计原则
- **RESTful规范**：遵循RESTful API设计规范
- **统一响应格式**：所有接口返回统一JSON格式
- **版本控制**：API路径包含版本号，/api/v1/
- **认证机制**：使用JWT Token进行身份认证
- **错误处理**：统一的错误码和错误信息

### 4.2 统一响应格式

#### 成功响应
{
  "code": 200,
  "message": "操作成功",
  "data": {
    // 业务数据
  },
  "timestamp": 1704067200000
}#### 错误响应
{
  "code": 400,
  "message": "请求参数错误",
  "error": "详细错误信息",
  "timestamp": 1704067200000
}#### 分页响应
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "list": [],
    "total": 100,
    "page": 1,
    "pageSize": 20,
    "totalPages": 5
  },
  "timestamp": 1704067200000
}### 4.3 供应商管理接口

#### 4.3.1 获取供应商列表
- **接口**：`GET /api/v1/suppliers`
- **请求参数**：
  - `page`: 页码（默认1）
  - `pageSize`: 每页数量（默认20）
  - `keyword`: 搜索关键词（可选）
  - `sortBy`: 排序字段（可选）
  - `sortOrder`: 排序方式（asc/desc，可选）
- **响应示例**：见下方Mock数据

#### 4.3.2 获取供应商详情
- **接口**：`GET /api/v1/suppliers/{id}`
- **响应示例**：见下方Mock数据

#### 4.3.3 创建供应商
- **接口**：`POST /api/v1/suppliers`
- **请求体**：
{
  "name": "供应商A",
  "contactName": "张三",
  "phone": "13800138000",
  "email": "zhangsan@example.com",
  "address": "北京市朝阳区xxx",
  "creditCode": "91110000MA01234567",
  "establishDate": "2020-01-01",
  "registeredCapital": "1000万元",
  "remark": "备注信息"
}#### 4.3.4 更新供应商
- **接口**：`PUT /api/v1/suppliers/{id}`
- **请求体**：同创建接口

#### 4.3.5 删除供应商
- **接口**：`DELETE /api/v1/suppliers/{id}`

#### 4.3.6 上传供应商名片图片
- **接口**：`POST /api/v1/suppliers/{id}/business-cards`
- **请求类型**：`multipart/form-data`
- **请求参数**：
  - `files`: 图片文件数组（支持多文件上传）

#### 4.3.7 获取工厂列表
- **接口**：`GET /api/v1/suppliers/{id}/factories`

#### 4.3.8 创建工厂
- **接口**：`POST /api/v1/suppliers/{id}/factories`
- **请求体**：
{
  "name": "工厂A",
  "address": "北京市xxx",
  "area": 5000,
  "employeeCount": 200,
  "productCategories": ["电子产品"],
  "productionCapacity": "月产量10000件",
  "certifications": ["ISO9001"],
  "contactPhone": "13800138001"
}#### 4.3.9 获取仓库列表
- **接口**：`GET /api/v1/suppliers/{id}/warehouses`

#### 4.3.10 创建仓库
- **接口**：`POST /api/v1/suppliers/{id}/warehouses`
- **请求体**：
{
  "name": "仓库A",
  "address": "北京市xxx",
  "latitude": 39.9042,
  "longitude": 116.4074,
  "area": 2000,
  "capacity": "10000立方米",
  "type": "成品仓"
}### 4.4 货品管理接口

#### 4.4.1 获取货品列表
- **接口**：`GET /api/v1/products`
- **请求参数**：
  - `page`: 页码
  - `pageSize`: 每页数量
  - `keyword`: 搜索关键词
  - `category`: 分类筛选
  - `supplierId`: 供应商ID筛选
  - `sortBy`: 排序字段
  - `sortOrder`: 排序方式

#### 4.4.2 获取货品详情
- **接口**：`GET /api/v1/products/{id}`

#### 4.4.3 创建货品
- **接口**：`POST /api/v1/products`
- **请求体**：on
{
  "productCode": "PROD001",
  "name": "货品名称",
  "category": "电子产品",
  "specification": "规格型号",
  "unit": "件",
  "price": 100.00,
  "stock": 1000,
  "minStock": 100,
  "minWholesaleQuantity": 10,
  "isClearance": false,
  "isNew": true,
  "supplierId": "supplier_001",
  "description": "货品描述"
}#### 4.4.4 更新货品
- **接口**：`PUT /api/v1/products/{id}`

#### 4.4.5 删除货品
- **接口**：`DELETE /api/v1/products/{id}`

#### 4.4.6 上传货品图片
- **接口**：`POST /api/v1/products/{id}/images`
- **请求类型**：`multipart/form-data`
- **请求参数**：
  - `files`: 图片文件数组（最多9张）

#### 4.4.7 上传货品视频
- **接口**：`POST /api/v1/products/{id}/videos`
- **请求类型**：`multipart/form-data`
- **请求参数**：
  - `file`: 视频文件（最多3个，单个不超过50MB）

### 4.5 采购单管理接口

#### 4.5.1 获取采购单列表
- **接口**：`GET /api/v1/purchase-orders`
- **请求参数**：
  - `page`: 页码
  - `pageSize`: 每页数量
  - `keyword`: 搜索关键词（采购单号）
  - `status`: 状态筛选（pending/approved/purchasing/completed/cancelled）
  - `startDate`: 开始日期
  - `endDate`: 结束日期
  - `supplierId`: 供应商ID筛选

#### 4.5.2 获取采购单详情
- **接口**：`GET /api/v1/purchase-orders/{id}`

#### 4.5.3 创建采购单
- **接口**：`POST /api/v1/purchase-orders`
- **请求体**：
{
  "orderNumber": "PO20240101001",
  "supplierId": "supplier_001",
  "purchaserId": "user_001",
  "purchaseDate": "2024-01-01",
  "expectedArrivalDate": "2024-01-15",
  "items": [
    {
      "productId": "product_001",
      "quantity": 100,
      "unitPrice": 50.00,
      "remark": "备注"
    }
  ],
  "remark": "采购单备注"
}#### 4.5.4 更新采购单
- **接口**：`PUT /api/v1/purchase-orders/{id}`
- **说明**：仅未审核状态可编辑

#### 4.5.5 删除采购单
- **接口**：`DELETE /api/v1/purchase-orders/{id}`

#### 4.5.6 审核采购单
- **接口**：`POST /api/v1/purchase-orders/{id}/approve`
- **请求体**：
{
  "approved": true,
  "remark": "审核备注"
}#### 4.5.7 更新采购单状态
- **接口**：`PUT /api/v1/purchase-orders/{id}/status`
- **请求体**：n
{
  "status": "purchasing"
}### 4.6 文件上传接口

#### 4.6.1 通用文件上传
- **接口**：`POST /api/v1/upload`
- **请求类型**：`multipart/form-data`
- **请求参数**：
  - `file`: 文件
  - `type`: 文件类型（image/video/document）
- **响应示例**：
{
  "code": 200,
  "message": "上传成功",
  "data": {
    "url": "https://example.com/files/xxx.jpg",
    "fileName": "xxx.jpg",
    "fileSize": 1024000,
    "fileType": "image/jpeg"
  }
}### 4.7 认证接口

#### 4.7.1 用户登录
- **接口**：`POST /api/v1/auth/login`
- **请求体**：
{
  "username": "admin",
  "password": "password123"
}- **响应示例**：
{
  "code": 200,
  "message": "登录成功",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "refresh_token_xxx",
    "expiresIn": 3600,
    "user": {
      "id": "user_001",
      "username": "admin",
      "name": "管理员"
    }
  }
}#### 4.7.2 刷新Token
- **接口**：`POST /api/v1/auth/refresh`
- **请求体**：
{
  "refreshToken": "refresh_token_xxx"
}#### 4.7.3 用户登出
- **接口**：`POST /api/v1/auth/logout`

## 5. Mock数据和接口测试数据结构体

### 5.1 Mock数据服务设计

#### 5.1.1 Mock数据存储
- 使用本地数据库（SQLite/关系型数据库）存储Mock数据
- 支持数据的增删改查操作
- 数据格式与真实API保持一致

#### 5.1.2 Mock API服务层
- 创建Mock API服务，模拟后端接口行为
- 支持延迟响应（模拟网络延迟）
- 支持错误场景模拟

### 5.2 供应商Mock数据

#### 5.2.1 供应商列表Mock数据
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "list": [
      {
        "id": "supplier_001",
        "name": "北京科技有限公司",
        "contactName": "张三",
        "phone": "13800138000",
        "email": "zhangsan@example.com",
        "address": "北京市朝阳区建国路88号",
        "creditCode": "91110000MA01234567",
        "establishDate": "2020-01-01",
        "registeredCapital": "1000万元",
        "remark": "长期合作伙伴",
        "businessCards": [
          "https://example.com/cards/card1.jpg",
          "https://example.com/cards/card2.jpg"
        ],
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-01T00:00:00Z"
      },
      {
        "id": "supplier_002",
        "name": "上海贸易有限公司",
        "contactName": "李四",
        "phone": "13900139000",
        "email": "lisi@example.com",
        "address": "上海市浦东新区世纪大道100号",
        "creditCode": "91310000MA02345678",
        "establishDate": "2019-05-15",
        "registeredCapital": "2000万元",
        "remark": "优质供应商",
        "businessCards": [
          "https://example.com/cards/card3.jpg"
        ],
        "createdAt": "2024-01-02T00:00:00Z",
        "updatedAt": "2024-01-02T00:00:00Z"
      }
    ],
    "total": 2,
    "page": 1,
    "pageSize": 20,
    "totalPages": 1
  },
  "timestamp": 1704067200000
}#### 5.2.2 供应商详情Mock数据
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "id": "supplier_001",
    "name": "北京科技有限公司",
    "contactName": "张三",
    "phone": "13800138000",
    "email": "zhangsan@example.com",
    "address": "北京市朝阳区建国路88号",
    "creditCode": "91110000MA01234567",
    "establishDate": "2020-01-01",
    "registeredCapital": "1000万元",
    "remark": "长期合作伙伴",
    "businessCards": [
      "https://example.com/cards/card1.jpg",
      "https://example.com/cards/card2.jpg"
    ],
    "factories": [
      {
        "id": "factory_001",
        "supplierId": "supplier_001",
        "name": "北京工厂A",
        "address": "北京市通州区工业园A区",
        "area": 5000,
        "employeeCount": 200,
        "productCategories": ["电子产品", "智能设备"],
        "productionCapacity": "月产量10000件",
        "certifications": ["ISO9001", "CE认证"],
        "contactPhone": "13800138001",
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-01T00:00:00Z"
      }
    ],
    "warehouses": [
      {
        "id": "warehouse_001",
        "supplierId": "supplier_001",
        "factoryId": "factory_001",
        "name": "成品仓库A",
        "address": "北京市通州区工业园A区仓库",
        "latitude": 39.9042,
        "longitude": 116.4074,
        "area": 2000,
        "capacity": "10000立方米",
        "type": "成品仓",
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-01T00:00:00Z"
      }
    ],
    "createdAt": "2024-01-01T00:00:00Z",
    "updatedAt": "2024-01-01T00:00:00Z"
  },
  "timestamp": 1704067200000
}### 5.3 货品Mock数据

#### 5.3.1 货品列表Mock数据
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "list": [
      {
        "id": "product_001",
        "productCode": "PROD001",
        "name": "智能手机X1",
        "category": "电子产品",
        "specification": "128GB/8GB",
        "unit": "件",
        "price": 2999.00,
        "stock": 1000,
        "minStock": 100,
        "minWholesaleQuantity": 10,
        "isClearance": false,
        "isNew": true,
        "supplierId": "supplier_001",
        "supplierName": "北京科技有限公司",
        "description": "高性能智能手机，支持5G网络",
        "images": [
          "https://example.com/products/prod001_1.jpg",
          "https://example.com/products/prod001_2.jpg"
        ],
        "videos": [
          "https://example.com/products/prod001_video.mp4"
        ],
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-01T00:00:00Z"
      },
      {
        "id": "product_002",
        "productCode": "PROD002",
        "name": "智能手表Y2",
        "category": "电子产品",
        "specification": "标准版",
        "unit": "件",
        "price": 899.00,
        "stock": 50,
        "minStock": 100,
        "minWholesaleQuantity": 5,
        "isClearance": false,
        "isNew": false,
        "supplierId": "supplier_001",
        "supplierName": "北京科技有限公司",
        "description": "健康监测智能手表",
        "images": [
          "https://example.com/products/prod002_1.jpg"
        ],
        "videos": [],
        "createdAt": "2024-01-02T00:00:00Z",
        "updatedAt": "2024-01-02T00:00:00Z"
      }
    ],
    "total": 2,
    "page": 1,
    "pageSize": 20,
    "totalPages": 1
  },
  "timestamp": 1704067200000
}#### 5.3.2 货品详情Mock数据
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "id": "product_001",
    "productCode": "PROD001",
    "name": "智能手机X1",
    "category": "电子产品",
    "specification": "128GB/8GB",
    "unit": "件",
    "price": 2999.00,
    "stock": 1000,
    "minStock": 100,
    "minWholesaleQuantity": 10,
    "isClearance": false,
    "isNew": true,
    "supplierId": "supplier_001",
    "supplierName": "北京科技有限公司",
    "description": "高性能智能手机，支持5G网络，配备最新处理器，6.5英寸全面屏，4800万像素后置三摄，支持快充功能。",
    "images": [
      "https://example.com/products/prod001_1.jpg",
      "https://example.com/products/prod001_2.jpg",
      "https://example.com/products/prod001_3.jpg"
    ],
    "videos": [
      "https://example.com/products/prod001_video.mp4"
    ],
    "createdAt": "2024-01-01T00:00:00Z",
    "updatedAt": "2024-01-01T00:00:00Z"
  },
  "timestamp": 1704067200000
}### 5.4 采购单Mock数据

#### 5.4.1 采购单列表Mock数据
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "list": [
      {
        "id": "order_001",
        "orderNumber": "PO20240101001",
        "supplierId": "supplier_001",
        "supplierName": "北京科技有限公司",
        "purchaserId": "user_001",
        "purchaserName": "采购员A",
        "purchaseDate": "2024-01-01",
        "expectedArrivalDate": "2024-01-15",
        "status": "pending",
        "statusText": "待审核",
        "totalAmount": 299900.00,
        "itemCount": 1,
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-01T00:00:00Z"
      },
      {
        "id": "order_002",
        "orderNumber": "PO20240102001",
        "supplierId": "supplier_002",
        "supplierName": "上海贸易有限公司",
        "purchaserId": "user_001",
        "purchaserName": "采购员A",
        "purchaseDate": "2024-01-02",
        "expectedArrivalDate": "2024-01-20",
        "status": "approved",
        "statusText": "已审核",
        "totalAmount": 44950.00,
        "itemCount": 2,
        "createdAt": "2024-01-02T00:00:00Z",
        "updatedAt": "2024-01-02T00:00:00Z"
      }
    ],
    "total": 2,
    "page": 1,
    "pageSize": 20,
    "totalPages": 1
  },
  "timestamp": 1704067200000
}#### 5.4.2 采购单详情Mock数据
{
  "code": 200,
  "message": "获取成功",
  "data": {
    "id": "order_001",
    "orderNumber": "PO20240101001",
    "supplierId": "supplier_001",
    "supplierName": "北京科技有限公司",
    "purchaserId": "user_001",
    "purchaserName": "采购员A",
    "purchaseDate": "2024-01-01",
    "expectedArrivalDate": "2024-01-15",
    "status": "pending",
    "statusText": "待审核",
    "totalAmount": 299900.00,
    "items": [
      {
        "id": "item_001",
        "productId": "product_001",
        "productName": "智能手机X1",
        "productCode": "PROD001",
        "quantity": 100,
        "unit": "件",
        "unitPrice": 2999.00,
        "subtotal": 299900.00,
        "remark": "首批采购"
      }
    ],
    "remark": "紧急采购，请尽快处理",
    "createdAt": "2024-01-01T00:00:00Z",
    "updatedAt": "2024-01-01T00:00:00Z"
  },
  "timestamp": 1704067200000
}### 5.5 接口测试数据结构体（TypeScript/ArkTS）

#### 5.5.1 供应商数据结构体pt
// Supplier.ts
export interface Supplier {
  id: string;
  name: string;
  contactName?: string;
  phone?: string;
  email?: string;
  address?: string;
  creditCode?: string;
  establishDate?: string;
  registeredCapital?: string;
  remark?: string;
  businessCards?: string[];
  factories?: Factory[];
  warehouses?: Warehouse[];
  createdAt: string;
  updatedAt: string;
}

export interface Factory {
  id: string;
  supplierId: string;
  name: string;
  address?: string;
  area?: number;
  employeeCount?: number;
  productCategories?: string[];
  productionCapacity?: string;
  certifications?: string[];
  contactPhone?: string;
  createdAt: string;
  updatedAt: string;
}

export interface Warehouse {
  id: string;
  supplierId: string;
  factoryId?: string;
  name: string;
  address?: string;
  latitude?: number;
  longitude?: number;
  area?: number;
  capacity?: string;
  type?: string;
  createdAt: string;
  updatedAt: string;
}#### 5.5.2 货品数据结构体cript
// Product.ts
export interface Product {
  id: string;
  productCode: string;
  name: string;
  category?: string;
  specification?: string;
  unit?: string;
  price?: number;
  stock?: number;
  minStock?: number;
  minWholesaleQuantity?: number;
  isClearance?: boolean;
  isNew?: boolean;
  supplierId?: string;
  supplierName?: string;
  description?: string;
  images?: string[];
  videos?: string[];
  createdAt: string;
  updatedAt: string;
}#### 5.5.3 采购单数据结构体
// PurchaseOrder.ts
export interface PurchaseOrder {
  id: string;
  orderNumber: string;
  supplierId: string;
  supplierName?: string;
  purchaserId: string;
  purchaserName?: string;
  purchaseDate: string;
  expectedArrivalDate?: string;
  status: PurchaseOrderStatus;
  statusText?: string;
  totalAmount: number;
  items: PurchaseOrderItem[];
  remark?: string;
  createdAt: string;
  updatedAt: string;
}

export interface PurchaseOrderItem {
  id: string;
  productId: string;
  productName?: string;
  productCode?: string;
  quantity: number;
  unit?: string;
  unitPrice: number;
  subtotal: number;
  remark?: string;
}

export enum PurchaseOrderStatus {
  PENDING = 'pending',
  APPROVED = 'approved',
  PURCHASING = 'purchasing',
  COMPLETED = 'completed',
  CANCELLED = 'cancelled'
}#### 5.5.4 API响应数据结构体
// ApiResponse.ts
export interface ApiResponse<T> {
  code: number;
  message: string;
  data?: T;
  error?: string;
  timestamp: number;
}

export interface PaginatedResponse<T> {
  list: T[];
  total: number;
  page: number;
  pageSize: number;
  totalPages: number;
}

export interface ApiResponseWithPagination<T> extends ApiResponse<PaginatedResponse<T>> {}
## 6. 开发实施

### 6.1 开发环境搭建

#### 6.1.1 HarmonyOS开发环境
1. 安装DevEco Studio
2. 配置HarmonyOS SDK
3. 配置华为开发者账号
4. 安装相关依赖包

#### 6.1.2 项目初始化
1. 创建HarmonyOS应用项目
2. 配置项目依赖（oh-package.json）
3. 配置应用权限（module.json5）
4. 初始化项目结构

### 6.2 开发阶段划分

#### 6.2.1 第一阶段：基础框架搭建（1-2周）
- 项目初始化和环境配置
- 导航结构搭建
- 基础UI组件库集成
- HTTP请求封装
- Mock数据服务搭建
- 本地存储配置
- 状态管理方案实现

#### 6.2.2 第二阶段：供应商管理模块（1-2周）
- 供应商列表页
- 供应商详情页
- 供应商新增/编辑页
- 名片图片上传功能
- 工厂信息管理
- 仓库位置管理（地图功能）

#### 6.2.3 第三阶段：货品管理模块（1-2周）
- 货品列表页
- 货品详情页
- 货品新增/编辑页
- 图片上传功能
- 视频上传功能
- 图片/视频预览功能

#### 6.2.4 第四阶段：采购单模块（1-2周）
- 采购单列表页
- 采购单详情页
- 采购单创建/编辑页
- 状态管理功能
- 采购明细管理

#### 6.2.5 第五阶段：优化和测试（1周）
- 性能优化
- UI/UX优化
- 华为手机适配测试
- Bug修复
- 代码审查

### 6.3 关键技术实现

#### 6.3.1 HTTP请求封装
- 统一请求拦截器（添加Token、设置请求头）
- 统一响应拦截器（错误处理、Token刷新）
- 请求重试机制
- 请求取消机制

#### 6.3.2 本地存储方案
- 使用关系型数据库存储结构化数据
- 使用Preferences存储配置信息
- 实现数据同步机制

#### 6.3.3 图片处理
- 图片压缩
- 图片缓存
- 图片懒加载
- 图片预览

#### 6.3.4 视频处理
- 视频选择
- 视频预览
- 视频上传进度显示

#### 6.3.5 地图功能
- 集成华为地图SDK
- 位置标注
- 导航功能

### 6.4 测试方案

#### 6.4.1 单元测试
- 工具类测试
- 服务层测试
- 组件测试

#### 6.4.2 集成测试
- API接口测试
- 数据流测试
- 功能测试

#### 6.4.3 端到端测试
- 用户流程测试
- 性能测试
- 兼容性测试

### 6.5 部署方案

#### 6.5.1 开发环境
- 本地Mock数据
- 开发调试工具

#### 6.5.2 测试环境
- 测试服务器
- 测试数据

#### 6.5.3 生产环境
- 生产服务器
- 生产配置
- 监控和日志

## 7. 安全方案

### 7.1 数据传输安全
- 使用HTTPS加密传输
- 证书校验
- 防止中间人攻击

### 7.2 身份认证
- JWT Token认证
- Token刷新机制
- 自动登录

### 7.3 数据存储安全
- 敏感数据加密存储
- 本地数据访问控制

### 7.4 权限控制
- 功能权限控制
- 数据权限控制

## 8. 性能优化

### 8.1 网络优化
- 请求合并
- 数据缓存
- 图片懒加载

### 8.2 渲染优化
- 列表虚拟滚动
- 图片压缩
- 组件懒加载

### 8.3 存储优化
- 数据分页加载
- 缓存策略
- 数据清理

## 9. 后续扩展

### 9.1 功能扩展
- 数据统计报表
- 导出功能（Excel、PDF）
- 消息推送通知
- 多用户权限管理
- 数据同步（云端备份）
- 扫码功能
- 审批流程
- 库存预警推送

### 9.2 技术扩展
- 离线数据同步
- 多端数据同步
- 实时通信
- 大数据分析

---

**文档版本**：v1.0  
**创建日期**：2024年  
**最后更新**：2024年
```

文档已按产品方案文档的需求编写，可直接使用。
```

