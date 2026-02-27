# Flutter 开发规范文档

> 版本：v1.0.0 | 最后更新：2026-02-27 | 适用范围：全体 Flutter 开发者

---

## 目录

1. [项目结构规范](#1-项目结构规范)
2. [命名规范](#2-命名规范)
3. [代码风格规范](#3-代码风格规范)
4. [Widget 编写规范](#4-widget-编写规范)
5. [状态管理规范](#5-状态管理规范)
6. [路由管理规范](#6-路由管理规范)
7. [网络请求规范](#7-网络请求规范)
8. [数据模型规范](#8-数据模型规范)
9. [异常处理规范](#9-异常处理规范)
10. [性能优化规范](#10-性能优化规范)
11. [主题与样式规范](#11-主题与样式规范)
12. [国际化规范](#12-国际化规范)
13. [测试规范](#13-测试规范)
14. [Git 提交规范](#14-git-提交规范)
15. [注释与文档规范](#15-注释与文档规范)
16. [依赖管理规范](#16-依赖管理规范)
17. [安全规范](#17-安全规范)

---

## 1. 项目结构规范

### 1.1 推荐目录结构

```
lib/
├── app/
│   ├── app.dart                  # App 根组件
│   └── routes.dart               # 路由配置
├── core/
│   ├── constants/                # 全局常量
│   │   ├── app_constants.dart
│   │   ├── api_constants.dart
│   │   └── asset_constants.dart
│   ├── errors/                   # 错误处理
│   │   ├── exceptions.dart
│   │   └── failures.dart
│   ├── extensions/               # Dart 扩展方法
│   ├── network/                  # 网络层基础配置
│   │   ├── dio_client.dart
│   │   └── interceptors/
│   ├── storage/                  # 本地存储
│   ├── theme/                    # 全局主题
│   │   ├── app_theme.dart
│   │   ├── app_colors.dart
│   │   └── app_text_styles.dart
│   └── utils/                    # 工具类
├── data/
│   ├── datasources/              # 数据源（remote/local）
│   │   ├── remote/
│   │   └── local/
│   ├── models/                   # 数据模型（含序列化）
│   └── repositories/             # Repository 实现层
├── domain/
│   ├── entities/                 # 业务实体
│   ├── repositories/             # Repository 抽象接口
│   └── usecases/                 # 业务用例
├── features/                     # 功能模块（按业务划分）
│   ├── auth/
│   │   ├── data/
│   │   ├── domain/
│   │   └── presentation/
│   │       ├── pages/
│   │       ├── widgets/
│   │       └── providers/ (or blocs/cubits)
│   └── home/
│       └── ...
├── shared/
│   ├── widgets/                  # 公共 Widget
│   └── mixins/                   # 公共 Mixin
└── main.dart
```

### 1.2 模块化原则

- 每个功能模块应高内聚、低耦合，包含自己的 `data`、`domain`、`presentation` 层。
- 公共组件放入 `shared/widgets/`，禁止在业务模块中直接相互引用。
- 全局工具、常量统一在 `core/` 目录下管理，禁止散落在业务模块中。

---

## 2. 命名规范

### 2.1 文件命名

| 类型 | 规则 | 示例 |
|------|------|------|
| Dart 文件 | `snake_case` | `user_profile_page.dart` |
| 图片资源 | `snake_case` | `ic_home_active.png` |
| 字体文件 | `PascalCase` | `PingFangSC-Regular.ttf` |

### 2.2 类命名

| 类型 | 规则 | 示例 |
|------|------|------|
| 类 / Widget | `PascalCase` | `UserProfilePage` |
| 枚举 | `PascalCase` | `LoadingStatus` |
| 扩展 | `PascalCase + Extension` | `StringExtension` |
| Mixin | `PascalCase + Mixin` | `ValidationMixin` |
| 抽象类 | `PascalCase` | `BaseRepository` |

### 2.3 变量与方法命名

| 类型 | 规则 | 示例 |
|------|------|------|
| 变量 | `camelCase` | `userName` |
| 私有变量 | `_camelCase` | `_isLoading` |
| 常量 | `camelCase` | `kDefaultPadding` |
| 方法 | `camelCase` | `fetchUserData()` |
| 布尔变量 | `is / has / can` 前缀 | `isLoggedIn`, `hasError` |

### 2.4 常量命名

```dart
// ✅ 正确：使用 k 前缀区分常量
const double kDefaultPadding = 16.0;
const int kAnimationDuration = 300;
const String kBaseUrl = 'https://api.example.com';

// ❌ 错误：与普通变量无区分
const double defaultPadding = 16.0;
```

---

## 3. 代码风格规范

### 3.1 基础规则

- 使用 `dart format` 格式化代码，CI 中强制执行。
- 每行代码不超过 **120 个字符**。
- 使用 2 个空格缩进（Dart 默认标准）。
- 文件末尾保留一个空行。
- 禁止提交含有 `print()` 的代码，统一使用日志工具（如 `logger` 包）。

### 3.2 import 顺序

按照以下顺序排列 import，各组之间保留一个空行：

```dart
// 1. Dart SDK
import 'dart:async';
import 'dart:io';

// 2. Flutter 包
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

// 3. 第三方包
import 'package:dio/dio.dart';
import 'package:riverpod/riverpod.dart';

// 4. 项目内部引用（绝对路径）
import 'package:my_app/core/theme/app_colors.dart';
import 'package:my_app/features/auth/presentation/pages/login_page.dart';
```

> **禁止**使用相对路径 import（如 `../widgets/button.dart`），统一使用 `package:` 绝对路径。

### 3.3 类型声明

```dart
// ✅ 明确声明类型
final String userName = 'Alice';
List<UserModel> users = [];

// ❌ 避免在成员变量上过度依赖类型推断
var users = []; // 类型不明确
```

### 3.4 级联操作符

```dart
// ✅ 对同一对象多次操作时使用级联
final paint = Paint()
  ..color = Colors.red
  ..strokeWidth = 2.0
  ..style = PaintingStyle.fill;

// ❌ 重复引用对象
final paint = Paint();
paint.color = Colors.red;
paint.strokeWidth = 2.0;
```

---

## 4. Widget 编写规范

### 4.1 优先使用 StatelessWidget

```dart
// ✅ 无状态时优先使用 StatelessWidget
class UserAvatar extends StatelessWidget {
  const UserAvatar({
    super.key,
    required this.imageUrl,
    this.size = 40.0,
  });

  final String imageUrl;
  final double size;

  @override
  Widget build(BuildContext context) {
    return CircleAvatar(
      radius: size / 2,
      backgroundImage: NetworkImage(imageUrl),
    );
  }
}
```

### 4.2 构造函数规范

- 所有 Widget 必须声明 `key` 参数（使用 `super.key`）。
- 必填参数使用 `required` 修饰，可选参数提供默认值。
- Widget 构造函数使用 `const` 修饰（当所有参数均为常量时）。

```dart
// ✅ 正确示例
class CustomButton extends StatelessWidget {
  const CustomButton({
    super.key,
    required this.label,
    required this.onPressed,
    this.color = Colors.blue,
    this.isLoading = false,
  });

  final String label;
  final VoidCallback onPressed;
  final Color color;
  final bool isLoading;

  @override
  Widget build(BuildContext context) { ... }
}
```

### 4.3 拆分 Widget

- 单个 `build()` 方法不超过 **60 行**。
- 复杂 UI 必须提取为独立 Widget 类，**禁止**使用私有方法返回 Widget。

```dart
// ❌ 错误：使用方法返回 Widget
Widget _buildHeader() {
  return Container(...);
}

// ✅ 正确：提取为独立 Widget 类
class _PageHeader extends StatelessWidget {
  const _PageHeader();

  @override
  Widget build(BuildContext context) {
    return Container(...);
  }
}
```

### 4.4 避免不必要的 Widget 嵌套

```dart
// ❌ 不必要的嵌套
Container(
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Text('Hello'),
  ),
)

// ✅ 合并属性
Container(
  padding: const EdgeInsets.all(16),
  child: const Text('Hello'),
)
```

### 4.5 使用 const 构造

```dart
// ✅ 尽可能使用 const，减少重建开销
const SizedBox(height: 16),
const Divider(),
const Text('固定文本'),
```

---

## 5. 状态管理规范

> 项目统一使用 **Riverpod** 作为状态管理方案。

### 5.1 Provider 分类

| Provider 类型 | 适用场景 |
|--------------|---------|
| `Provider` | 只读、不变的值（如配置、工具类） |
| `StateProvider` | 简单状态（如计数、开关） |
| `StateNotifierProvider` | 复杂状态，含业务逻辑 |
| `FutureProvider` | 异步一次性数据 |
| `StreamProvider` | 持续数据流 |
| `AsyncNotifierProvider` | 异步状态管理（推荐新代码使用） |

### 5.2 StateNotifier 规范

```dart
// ✅ 标准 StateNotifier 写法
@riverpod
class UserNotifier extends _$UserNotifier {
  @override
  AsyncValue<UserEntity> build() => const AsyncValue.loading();

  Future<void> fetchUser(String userId) async {
    state = const AsyncValue.loading();
    state = await AsyncValue.guard(
      () => ref.read(getUserUseCaseProvider).call(userId),
    );
  }

  void clearUser() {
    state = const AsyncValue.data(UserEntity.empty());
  }
}
```

### 5.3 状态原则

- **状态不可变**：使用 `copyWith` 创建新实例，禁止直接修改状态对象属性。
- **单一数据源**：同一数据只在一个 Provider 中管理，其他地方通过 `ref.watch` 订阅。
- **UI 与逻辑分离**：业务逻辑必须在 Notifier 中，Widget 只负责展示。

---

## 6. 路由管理规范

> 项目统一使用 **GoRouter** 管理路由。

### 6.1 路由定义

```dart
// core/routes/app_router.dart
final appRouterProvider = Provider<GoRouter>((ref) {
  return GoRouter(
    initialLocation: AppRoutes.home,
    routes: [
      GoRoute(
        path: AppRoutes.login,
        name: AppRoutes.loginName,
        builder: (context, state) => const LoginPage(),
      ),
      ShellRoute(
        builder: (context, state, child) => MainShell(child: child),
        routes: [
          GoRoute(
            path: AppRoutes.home,
            name: AppRoutes.homeName,
            builder: (context, state, ) => const HomePage(),
          ),
        ],
      ),
    ],
  );
});

// 路由路径统一在常量类中管理
abstract class AppRoutes {
  static const String home = '/home';
  static const String homeName = 'home';
  static const String login = '/login';
  static const String loginName = 'login';
  static const String userDetail = '/user/:id';
  static const String userDetailName = 'userDetail';
}
```

### 6.2 路由跳转

```dart
// ✅ 使用 name 跳转，避免硬编码路径
context.goNamed(AppRoutes.homeName);
context.pushNamed(
  AppRoutes.userDetailName,
  pathParameters: {'id': userId},
);

// ❌ 避免硬编码路径字符串
context.go('/user/123');
```

---

## 7. 网络请求规范

### 7.1 Dio 封装

```dart
// core/network/dio_client.dart
class DioClient {
  DioClient(this._dio) {
    _dio
      ..options.baseUrl = ApiConstants.baseUrl
      ..options.connectTimeout = const Duration(seconds: 15)
      ..options.receiveTimeout = const Duration(seconds: 15)
      ..interceptors.addAll([
        AuthInterceptor(),
        LoggingInterceptor(),
        RetryInterceptor(dio: _dio),
      ]);
  }

  final Dio _dio;
}
```

### 7.2 数据源层规范

```dart
abstract class UserRemoteDataSource {
  Future<UserModel> getUser(String id);
  Future<List<UserModel>> getUserList();
}

class UserRemoteDataSourceImpl implements UserRemoteDataSource {
  const UserRemoteDataSourceImpl(this._client);

  final DioClient _client;

  @override
  Future<UserModel> getUser(String id) async {
    final response = await _client.get('/users/$id');
    return UserModel.fromJson(response.data as Map<String, dynamic>);
  }
}
```

### 7.3 Repository 层规范

```dart
// Repository 必须捕获底层异常并转换为业务 Failure
class UserRepositoryImpl implements UserRepository {
  const UserRepositoryImpl(this._remoteDataSource, this._localDataSource);

  final UserRemoteDataSource _remoteDataSource;
  final UserLocalDataSource _localDataSource;

  @override
  Future<Either<Failure, UserEntity>> getUser(String id) async {
    try {
      final model = await _remoteDataSource.getUser(id);
      await _localDataSource.cacheUser(model);
      return Right(model.toEntity());
    } on ServerException catch (e) {
      return Left(ServerFailure(e.message));
    } on NetworkException {
      // 尝试从本地缓存读取
      final cached = await _localDataSource.getCachedUser(id);
      if (cached != null) return Right(cached.toEntity());
      return const Left(NetworkFailure());
    }
  }
}
```

---

## 8. 数据模型规范

### 8.1 Model 与 Entity 分离

| 层级 | 类型 | 职责 |
|------|------|------|
| `domain/entities/` | Entity | 纯业务对象，不含序列化逻辑 |
| `data/models/` | Model | 继承或包含 Entity，负责 JSON 序列化 |

```dart
// domain/entities/user_entity.dart
class UserEntity extends Equatable {
  const UserEntity({
    required this.id,
    required this.name,
    required this.email,
  });

  final String id;
  final String name;
  final String email;

  @override
  List<Object?> get props => [id, name, email];
}

// data/models/user_model.dart
@JsonSerializable()
class UserModel extends UserEntity {
  const UserModel({
    required super.id,
    required super.name,
    required super.email,
    this.avatarUrl,
  });

  final String? avatarUrl;

  factory UserModel.fromJson(Map<String, dynamic> json) =>
      _$UserModelFromJson(json);

  Map<String, dynamic> toJson() => _$UserModelToJson(this);

  UserEntity toEntity() => UserEntity(id: id, name: name, email: email);
}
```

### 8.2 使用 Equatable

所有 Entity 和 State 类必须继承 `Equatable` 或实现 `==` 和 `hashCode`：

```dart
class UserState extends Equatable {
  const UserState({
    this.user,
    this.isLoading = false,
    this.error,
  });

  final UserEntity? user;
  final bool isLoading;
  final String? error;

  UserState copyWith({
    UserEntity? user,
    bool? isLoading,
    String? error,
  }) {
    return UserState(
      user: user ?? this.user,
      isLoading: isLoading ?? this.isLoading,
      error: error ?? this.error,
    );
  }

  @override
  List<Object?> get props => [user, isLoading, error];
}
```

---

## 9. 异常处理规范

### 9.1 异常层级

```dart
// core/errors/exceptions.dart（Data 层抛出）
class AppException implements Exception {
  const AppException(this.message);
  final String message;
}

class ServerException extends AppException {
  const ServerException(super.message, {this.statusCode});
  final int? statusCode;
}

class NetworkException extends AppException {
  const NetworkException() : super('网络连接失败，请检查网络');
}

class CacheException extends AppException {
  const CacheException(super.message);
}

// core/errors/failures.dart（Domain 层使用）
abstract class Failure extends Equatable {
  const Failure([this.message = '']);
  final String message;

  @override
  List<Object> get props => [message];
}

class ServerFailure extends Failure {
  const ServerFailure([super.message = '服务器错误']);
}

class NetworkFailure extends Failure {
  const NetworkFailure() : super('网络连接失败');
}
```

### 9.2 UI 层错误展示

```dart
// Widget 中统一处理 AsyncValue 的三种状态
ref.watch(userProvider).when(
  data: (user) => UserCard(user: user),
  loading: () => const CircularProgressIndicator(),
  error: (error, stack) => ErrorWidget(
    message: error is Failure ? error.message : '发生未知错误',
    onRetry: () => ref.invalidate(userProvider),
  ),
);
```

---

## 10. 性能优化规范

### 10.1 Widget 重建优化

```dart
// ✅ 使用 select 精准订阅，避免不必要的重建
final userName = ref.watch(userProvider.select((state) => state.user?.name));

// ❌ 订阅整个状态，任何变化都会触发重建
final userState = ref.watch(userProvider);
```

### 10.2 列表性能

```dart
// ✅ 长列表使用 ListView.builder
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) => ItemCard(item: items[index]),
)

// ✅ 固定高度列表使用 itemExtent 优化
ListView.builder(
  itemCount: items.length,
  itemExtent: 72.0,
  itemBuilder: (context, index) => ItemCard(item: items[index]),
)

// ❌ 禁止在列表中使用 shrinkWrap: true（性能开销大）
```

### 10.3 图片优化

```dart
// ✅ 指定 cacheWidth / cacheHeight 减少内存占用
Image.network(
  url,
  cacheWidth: 200,
  cacheHeight: 200,
)

// ✅ 使用 cached_network_image 缓存网络图片
CachedNetworkImage(
  imageUrl: url,
  placeholder: (context, url) => const ShimmerBox(),
  errorWidget: (context, url, error) => const Icon(Icons.error),
)
```

### 10.4 避免重复计算

```dart
// ✅ 使用 RepaintBoundary 隔离高频重绘区域
RepaintBoundary(
  child: AnimatedWidget(),
)

// ✅ 耗时计算使用 compute 在 Isolate 中执行
final result = await compute(parseJsonList, jsonString);
```

---

## 11. 主题与样式规范

### 11.1 禁止硬编码颜色和尺寸

```dart
// ❌ 禁止硬编码
Text(
  'Hello',
  style: TextStyle(color: Color(0xFF333333), fontSize: 16),
)

// ✅ 使用 Theme 和统一常量
Text(
  'Hello',
  style: Theme.of(context).textTheme.bodyLarge,
)
```

### 11.2 颜色定义

```dart
// core/theme/app_colors.dart
abstract class AppColors {
  // Brand Colors
  static const Color primary = Color(0xFF6C63FF);
  static const Color primaryLight = Color(0xFF9D97FF);
  static const Color primaryDark = Color(0xFF3D35CC);

  // Semantic Colors
  static const Color success = Color(0xFF4CAF50);
  static const Color warning = Color(0xFFFF9800);
  static const Color error = Color(0xFFF44336);
  static const Color info = Color(0xFF2196F3);

  // Neutral Colors
  static const Color textPrimary = Color(0xFF1A1A2E);
  static const Color textSecondary = Color(0xFF6B7280);
  static const Color border = Color(0xFFE5E7EB);
  static const Color background = Color(0xFFF9FAFB);
}
```

### 11.3 字间距与字体

```dart
// core/theme/app_text_styles.dart
abstract class AppTextStyles {
  static const TextStyle heading1 = TextStyle(
    fontSize: 28,
    fontWeight: FontWeight.w700,
    height: 1.3,
    color: AppColors.textPrimary,
  );

  static const TextStyle body = TextStyle(
    fontSize: 14,
    fontWeight: FontWeight.w400,
    height: 1.6,
    color: AppColors.textPrimary,
  );
}
```

---

## 12. 国际化规范

- 使用 Flutter 官方 `intl` + `flutter_gen` 方案。
- 所有用户可见字符串必须通过 `AppLocalizations.of(context)!.xxx` 获取，**禁止**在代码中硬编码中文或其他语言文本。
- ARB 文件命名规则：`app_zh.arb`、`app_en.arb`。

```dart
// ✅ 正确
Text(AppLocalizations.of(context)!.welcomeMessage)

// ❌ 错误
Text('欢迎使用')
```

---

## 13. 测试规范

### 13.1 测试覆盖要求

| 层级 | 测试类型 | 覆盖率要求 |
|------|---------|----------|
| Domain（UseCase/Entity） | 单元测试 | ≥ 90% |
| Data（Repository/DataSource） | 单元测试（Mock） | ≥ 80% |
| Presentation（Notifier/Widget） | 单元测试 + Widget 测试 | ≥ 70% |
| 核心用户流程 | 集成测试 | 全覆盖 |

### 13.2 测试文件结构

```
test/
├── unit/
│   ├── domain/
│   │   └── usecases/
│   └── data/
│       ├── models/
│       └── repositories/
├── widget/
│   └── features/
│       └── auth/
└── integration/
    └── flows/
```

### 13.3 单元测试示例

```dart
void main() {
  group('GetUserUseCase', () {
    late MockUserRepository mockRepository;
    late GetUserUseCase useCase;

    setUp(() {
      mockRepository = MockUserRepository();
      useCase = GetUserUseCase(mockRepository);
    });

    test('应该返回用户数据当 Repository 调用成功时', () async {
      // Arrange
      const userId = 'user_001';
      const expectedUser = UserEntity(id: userId, name: 'Alice', email: 'alice@example.com');
      when(() => mockRepository.getUser(userId))
          .thenAnswer((_) async => const Right(expectedUser));

      // Act
      final result = await useCase(userId);

      // Assert
      expect(result, const Right(expectedUser));
      verify(() => mockRepository.getUser(userId)).called(1);
    });

    test('应该返回 ServerFailure 当网络请求失败时', () async {
      // Arrange
      when(() => mockRepository.getUser(any()))
          .thenAnswer((_) async => const Left(ServerFailure()));

      // Act
      final result = await useCase('invalid_id');

      // Assert
      expect(result, const Left(ServerFailure()));
    });
  });
}
```

---

## 14. Git 提交规范

### 14.1 Commit Message 格式

遵循 [Conventional Commits](https://www.conventionalcommits.org/) 规范：

```
<type>(<scope>): <subject>

[可选 body]

[可选 footer]
```

### 14.2 Type 类型

| Type | 含义 |
|------|------|
| `feat` | 新功能 |
| `fix` | Bug 修复 |
| `docs` | 文档更新 |
| `style` | 代码格式（不影响逻辑） |
| `refactor` | 重构（非 feat 非 fix） |
| `perf` | 性能优化 |
| `test` | 测试相关 |
| `chore` | 构建/工具链/依赖更新 |
| `revert` | 回滚提交 |

### 14.3 示例

```
feat(auth): 添加 Google 登录功能

使用 google_sign_in 包实现第三方登录，
支持 iOS 和 Android 平台。

Closes #42
```

### 14.4 分支命名规范

| 类型 | 命名规则 | 示例 |
|------|---------|------|
| 功能开发 | `feat/描述` | `feat/user-profile` |
| Bug 修复 | `fix/描述` | `fix/login-crash` |
| 紧急修复 | `hotfix/描述` | `hotfix/payment-error` |
| 发布准备 | `release/版本号` | `release/1.2.0` |

---

## 15. 注释与文档规范

### 15.1 注释原则

- 代码应自文档化，避免废话注释（解释"做了什么"而非"为什么"）。
- 复杂逻辑、特殊处理、Hack 方案必须添加注释说明原因。
- 公共 API、Widget 必须添加 `///` 文档注释。

```dart
// ❌ 废话注释
// 设置 isLoading 为 true
setState(() => isLoading = true);

// ✅ 有价值的注释
// 由于 iOS 键盘遮挡问题，需要手动延迟 200ms 后滚动到底部
// 参考: https://github.com/flutter/flutter/issues/XXXXX
await Future.delayed(const Duration(milliseconds: 200));
scrollController.animateTo(...);
```

### 15.2 文档注释

```dart
/// 用户头像组件
///
/// 展示用户的圆形头像，支持网络图片和本地图片。
/// 当图片加载失败时展示默认占位头像。
///
/// 示例：
/// ```dart
/// UserAvatar(
///   imageUrl: 'https://example.com/avatar.png',
///   size: 56,
/// )
/// ```
class UserAvatar extends StatelessWidget { ... }
```

---

## 16. 依赖管理规范

### 16.1 引入第三方包原则

在引入新的第三方包前，必须评估以下条件：

- **维护活跃度**：近 6 个月有更新，pub.dev 评分 ≥ 120 分（likes）。
- **Flutter 兼容性**：支持当前项目的 Flutter 最低版本要求。
- **必要性**：确认是否有更轻量的原生实现方案。
- **License**：确认许可证与项目兼容（优先 MIT/Apache 2.0）。

### 16.2 版本锁定策略

```yaml
# pubspec.yaml
dependencies:
  # ✅ 指定兼容范围，允许小版本升级
  dio: ^5.4.0
  riverpod: ^2.5.0

  # ✅ 对关键包锁定到精确版本，避免意外升级
  flutter_secure_storage: 9.2.2

dev_dependencies:
  flutter_test:
    sdk: flutter
  mocktail: ^1.0.3
  build_runner: ^2.4.8
```

### 16.3 禁用的包

| 禁止使用 | 替代方案 |
|---------|---------|
| `get` (GetX) | Riverpod + GoRouter |
| `provider`（新代码） | Riverpod |
| `http`（新代码） | dio |

---

## 17. 安全规范

### 17.1 敏感数据存储

```dart
// ✅ 使用 flutter_secure_storage 存储 Token
final secureStorage = FlutterSecureStorage();
await secureStorage.write(key: 'access_token', value: token);

// ❌ 禁止使用 SharedPreferences 存储敏感信息
await prefs.setString('access_token', token); // 不安全！
```

### 17.2 API 安全

- API Key、Secret 等敏感配置禁止硬编码在代码中，使用环境变量或 `--dart-define` 注入。
- 所有网络请求必须走 HTTPS，禁止在生产环境关闭证书校验。

```dart
// ✅ 通过 --dart-define 注入
const apiKey = String.fromEnvironment('API_KEY');

// ❌ 硬编码
const apiKey = 'sk-xxxxxxxxxxxxxxxx'; // 严禁！
```

### 17.3 用户输入安全

- 所有用户输入必须在展示前做 XSS 转义处理（使用 WebView 时尤为重要）。
- 文件上传需校验文件类型和大小，防止恶意文件。

---

## 附录：Lint 规则配置

在 `analysis_options.yaml` 中启用以下规则：

```yaml
include: package:flutter_lints/flutter.yaml

analyzer:
  errors:
    missing_required_param: error
    missing_return: error
    dead_code: warning
  exclude:
    - "**/*.g.dart"
    - "**/*.freezed.dart"

linter:
  rules:
    - always_use_package_imports
    - avoid_print
    - avoid_unnecessary_containers
    - prefer_const_constructors
    - prefer_const_declarations
    - prefer_final_fields
    - prefer_final_locals
    - sized_box_for_whitespace
    - use_super_parameters
    - avoid_dynamic_calls
    - cancel_subscriptions
    - close_sinks
```

---

*本文档由团队共同维护，如有疑问或建议，请提交 Issue 或 PR 至文档仓库。*
