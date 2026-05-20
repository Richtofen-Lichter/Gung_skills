# 语言特定分析策略

不同编程语言的运行时特性、模块系统和惯用模式差异很大。阅读源码时，需要针对语言特点调整分析重点。

---

## Go

**重点关注**：

- **Goroutine / Channel 通信**：搜索 `go func`、`go keyword`、`chan` 类型声明。追踪 goroutine 的创建点和 channel 的发送/接收端，理解并发模型。注意 `select` 语句的多路复用逻辑。
- **Interface 多态**：Go 的 interface 是隐式满足的。搜索 `interface \{` 定义，然后反向搜索哪些 struct 实现了这些方法。关键模式：接受 interface、返回 struct。
- **Struct Embedding**：`type Foo struct { Bar }` 构成"伪继承"。追踪嵌入类型的字段和方法提升（promotion），区分组合与真正的继承。
- **`init()` 执行顺序**：`init()` 在包导入时隐式执行。按 import 依赖图追踪 `init()` 调用链，理解初始化顺序。
- **依赖注入模式**：Go 社区常用 Wire、Fx 或手动 DI。关注 `New*()` 构造函数如何组装依赖。
- **错误处理流**：`if err != nil` 是控制流的一部分。错误包裹用 `fmt.Errorf("...: %w", err)`，`errors.Is/As` 做类型判断。

**入口点识别**：`func main()` in `main` package，或 `cmd/` 目录下的多个 main。

---

## Rust

**重点关注**：

- **Ownership & Borrowing 对架构的影响**：理解哪些类型实现了 `Clone`/`Copy`，哪里用了 `Arc<Mutex<T>>` 做共享。借用的生命周期 `'a` 标注揭示数据流动方向。
- **Trait 体系**：搜索 `trait X \{` 定义，然后用 `impl X for Y` 追踪实现。关注孤儿规则对扩展方式的影响。Trait bound `<T: X>` 决定了泛型约束。
- **Async Runtime**：搜索 `#[tokio::main]`、`#[async_std]`、`.await` 调用点。理解 reactor 和 executor 的调度模型。`tokio::spawn` 和 `JoinHandle` 是并发分叉点。
- **宏展开**：`#[derive(...)]` 自动生成代码。声明宏 `macro_rules!` 和过程宏 `#[proc_macro]` 会生成隐式代码。阅读前先跑 `cargo expand` 或看宏定义。
- **模块系统**：`mod` / `pub mod` / `use` 构成 crate 内依赖图。`pub(crate)` vs `pub` 区分内外接口。
- **Result / Option 处理**：`?` 运算符是隐式 return。`match` / `if let` 是主要的分支拆包方式。

**入口点识别**：`fn main()` 或 `src/main.rs`；库的入口是 `src/lib.rs`。

---

## Python

**重点关注**：

- **装饰器链**：`@decorator` 的等价形式是 `func = decorator(func)`。多层装饰器从内到外套用。关注装饰器如何修改函数签名和行为。
- **`__init__` 与继承链**：`super().__init__()` 的调用顺序决定了多继承下的初始化链（MRO）。关键模式：`super()` 在多重继承下的协作。
- **Asyncio 事件循环**：搜索 `async def` / `await` / `asyncio.gather` / `create_task`。理解协程调度和事件循环的单线程并发模型。
- **动态类型追踪**：Python 无静态类型声明。追踪参数类型靠：类型注解（`def foo(x: int)`）、运行时 `isinstance` 检查、duck typing 的 `hasattr` / `__special_methods__`。
- **魔术方法**：`__call__`、`__getattr__`、`__iter__` 等让对象行为变得隐式。搜索 `__` 开头的方法理解对象协议。
- **Import 系统**：`__init__.py` 控制包的导出。`__all__` 列表定义了 `from module import *` 的公共 API。

**入口点识别**：`if __name__ == "__main__":` 块、`setup.py` / `pyproject.toml` 的 `console_scripts` 入口、`app.run()` 等框架启动调用。

---

## TypeScript / JavaScript

**重点关注**：

- **事件循环与异步控制流**：`Promise.then/.catch`、`async/await`、`setTimeout/setImmediate`、`process.nextTick`（Node.js）。理解 microtask vs macrotask 的执行顺序。
- **中间件模式**：Express/Koa/Redux 中使用 `(req, res, next) =>` 或 `store => next => action =>` 的洋葱模型。追踪 `next()` 调用前后的逻辑。
- **模块导入追踪**：ESM (`import/export`) 是静态的、可 tree-shakeable；CJS (`require/module.exports`) 是动态的。`index.ts` barrel exports 是常见的重导出聚合点。
- **组件树（React/Vue）**：React 的 `function Component()` 和 hooks (`useState`/`useEffect`)；Vue 的 `defineComponent` 和 composition API。从顶层组件向下追踪 props 传递和事件冒泡。
- **依赖注入（NestJS/Angular）**：`@Injectable()` + 构造函数参数是 DI 入口。NestJS 的 Module 系统通过 `providers` 注册。
- **类型系统（TS）**：`interface` vs `type`、泛型约束 `<T extends X>`、条件类型 `T extends U ? A : B`。类型定义文件 `.d.ts` 是公共 API 契约。

**入口点识别**：`package.json` 的 `main` / `module` / `bin` 字段；`index.ts` / `main.ts`；框架特定的 `main.ts`（NestJS）、`_app.tsx`（Next.js）。

---

## Java / Kotlin

**重点关注**：

- **DI 容器追踪**：Spring 的 `@Autowired` / `@Bean` / `@Component` 定义 bean，通过 `ApplicationContext` 组装。追踪 bean 的依赖图和生命周期（`@PostConstruct` / `@PreDestroy`）。
- **注解处理器**：`@Entity`、`@Table`（JPA）、`@RestController`（Spring MVC）等注解在编译时或运行时生成隐式代码。需理解注解的处理机制（APT / Reflection）。
- **AOP 切面**：`@Aspect` + `@Around` / `@Before` / `@After` 织入横切逻辑。追踪切面的 pointcut 表达式，理解拦截的实际方法范围。
- **继承与接口**：`abstract class` vs `interface`、`implements` vs `extends`。关注模板方法模式和策略模式的运用。
- **异常处理链**：checked exception（`throws`）显式声明异常传递；unchecked 隐式传播。`try-with-resources` 自动关闭资源。
- **构建系统**：Maven (`pom.xml`) 的依赖树和插件；Gradle (`build.gradle`) 的 task 依赖图。

**入口点识别**：`public static void main(String[] args)`；Spring Boot 的 `@SpringBootApplication` 类；`web.xml` 或 Servlet 配置。

---

## C / C++

**重点关注**：

- **头文件组织**：`.h` 声明接口、`.c/.cpp` 实现。追踪 `#include` 依赖图，注意 forward declaration 的依赖解耦技巧和 include guard / `#pragma once`。
- **内存管理约定**：关键问题——"谁分配、谁释放？"。关注 `malloc/free`（C）、`new/delete`（C++）、`std::unique_ptr` / `std::shared_ptr`（C++11+）。RAII 模式：构造获取、析构释放。
- **虚函数与多态**：`virtual` 函数构成 vtable。追踪基类指针/引用调用派生类实现的情况。纯虚函数 `= 0` 定义接口契约。
- **构建系统**：CMake (`CMakeLists.txt`)、Make (`Makefile`)、Bazel (`BUILD`)。理解构建目标和链接关系（static vs shared library）。
- **模板与泛型（C++）**：`template<typename T>` 是编译期多态。SFINAE 和 concept（C++20）控制重载决议。模板实例化点决定可见的代码路径。
- **信号/中断处理**：`signal()` / `sigaction()` 注册异步处理。理解信号安全函数限制。

**入口点识别**：`int main(int argc, char* argv[])`；`CMakeLists.txt` 中的 `add_executable` 目标；动态库的导出符号。
