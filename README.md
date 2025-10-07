# TypeScript Complete Guide: Beginner to Advanced

## Table of Contents
1. [Setup & Configuration](#setup--configuration)
2. [Type System Fundamentals](#type-system-fundamentals)
3. [Advanced Types](#advanced-types)
4. [Functions](#functions)
5. [Objects & Interfaces](#objects--interfaces)
6. [Classes & OOP](#classes--oop)
7. [Generics](#generics)
8. [Utility Types](#utility-types)
9. [Real-world Patterns](#real-world-patterns)

---

## Setup & Configuration

### Installation & Basic Setup

```bash
# Install TypeScript globally
npm install -g typescript

# Check version
tsc --version

# Initialize TypeScript configuration
tsc --init
```

### tsconfig.json Deep Dive

```json
{
  "compilerOptions": {
    "rootDir": "./src",
    "outDir": "./dist",
    "target": "ES2020",
    "module": "CommonJS",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### Compilation Commands

```bash
# Compile specific file
tsc index.ts

# Watch mode for specific file
tsc index.ts -w

# Compile with custom output
tsc index.ts --outFile dist/bundle.js

# Compile entire project
tsc

# Watch entire project
tsc -w

# Production build with stricter checks
tsc --noEmitOnError --incremental
```

---

## Type System Fundamentals

### Primitive Types

```typescript
// Explicit type annotations
let username: string = 'alice';
let age: number = 30;
let isActive: boolean = true;
let bigNumber: bigint = 9007199254740991n;
let uniqueId: symbol = Symbol('id');

// Type inference - TypeScript automatically infers types
let inferredString = 'hello'; // Type: string
let inferredNumber = 42;      // Type: number
```

### Union Types

```typescript
// Variable can hold multiple types
let userId: string | number;
userId = 'abc123';    // OK
userId = 123;         // OK
userId = true;        // Error

// Real-world example: API response that can be success or error
type ApiResponse = { data: User } | { error: string };

function handleResponse(response: ApiResponse) {
    if ('error' in response) {
        console.error(response.error);
    } else {
        console.log(response.data);
    }
}
```

### Literal Types

```typescript
// Specific string values
let status: 'pending' | 'success' | 'error';
status = 'pending';  // OK
status = 'failed';   // Error

// Numeric literals
let diceRoll: 1 | 2 | 3 | 4 | 5 | 6;
diceRoll = 3;  // OK
diceRoll = 7;  // Error

// Real-world example: HTTP methods
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE' | 'PATCH';

async function apiRequest(method: HttpMethod, url: string) {
    return fetch(url, { method });
}
```

### Arrays and Tuples

```typescript
// Arrays
let numbers: number[] = [1, 2, 3];
let names: Array<string> = ['Alice', 'Bob']; // Generic syntax

// Readonly arrays
const readOnlyNames: readonly string[] = ['Alice', 'Bob'];
readOnlyNames.push('Charlie'); // Error

// Tuples - fixed length arrays with specific types
let user: [string, number, boolean] = ['Alice', 30, true];

// Real-world example: RGB color
type RGB = [number, number, number];
const red: RGB = [255, 0, 0];

// Optional tuple elements
type OptionalTuple = [string, number?];
const tuple1: OptionalTuple = ['hello'];
const tuple2: OptionalTuple = ['hello', 42];
```

---

## Advanced Types

### Type Aliases vs Interfaces

```typescript
// Type Alias - can represent primitives, unions, tuples
type ID = string | number;
type Point = {
    x: number;
    y: number;
};
type Callback<T> = (data: T) => void;

// Interface - primarily for object shapes
interface User {
    id: ID;
    name: string;
    email: string;
}

// Interface extension
interface Admin extends User {
    permissions: string[];
}

// Type intersection
type AdminUser = User & { permissions: string[] };
```

### Index Signatures & Record Types

```typescript
// Index signature - flexible object properties
interface StringDictionary {
    [key: string]: string;
}

const headers: StringDictionary = {
    'Content-Type': 'application/json',
    Authorization: 'Bearer token'
};

// Record type - predefined key-value types
type UserRoles = Record<string, 'admin' | 'user' | 'guest'>;

const roles: UserRoles = {
    alice: 'admin',
    bob: 'user'
};

// Real-world example: API configuration
interface ApiConfig {
    baseURL: string;
    timeout: number;
    headers: Record<string, string>;
}
```

### Conditional Types

```typescript
// Basic conditional type
type IsString<T> = T extends string ? true : false;
type A = IsString<'hello'>; // true
type B = IsString<42>;      // false

// Extract and Exclude utility types (built-in but here's how they work)
type MyExtract<T, U> = T extends U ? T : never;
type MyExclude<T, U> = T extends U ? never : T;

// Real-world example: Filter object properties by type
type StringKeys<T> = {
    [K in keyof T]: T[K] extends string ? K : never;
}[keyof T];

interface User {
    id: string;
    name: string;
    age: number;
    email: string;
}

type UserStringKeys = StringKeys<User>; // "id" | "name" | "email"
```

### Mapped Types

```typescript
// Make all properties optional
type Partial<T> = {
    [P in keyof T]?: T[P];
};

// Make all properties required
type Required<T> = {
    [P in keyof T]-?: T[P];
};

// Make all properties readonly
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

// Real-world example: API update payload
interface Product {
    id: string;
    name: string;
    price: number;
    category: string;
}

type ProductUpdate = Partial<Product>;
// Equivalent to:
// {
//     id?: string;
//     name?: string;
//     price?: number;
//     category?: string;
// }

const update: ProductUpdate = {
    price: 29.99  // Only update price
};
```

---

## Functions

### Function Declarations

```typescript
// Named function with types
function add(a: number, b: number): number {
    return a + b;
}

// Arrow function
const multiply = (a: number, b: number): number => a * b;

// Optional parameters
function greet(name: string, greeting?: string): string {
    return `${greeting || 'Hello'}, ${name}!`;
}

// Default parameters
function createUser(
    name: string, 
    role: 'admin' | 'user' = 'user'
): User {
    return { name, role };
}

// Rest parameters
function sum(...numbers: number[]): number {
    return numbers.reduce((total, num) => total + num, 0);
}
```

### Function Overloads

```typescript
// Function overloads - multiple call signatures
function processInput(input: string): string[];
function processInput(input: number): number[];
function processInput(input: string | number): string[] | number[] {
    if (typeof input === 'string') {
        return input.split('');
    } else {
        return input.toString().split('').map(Number);
    }
}

const stringResult = processInput('hello'); // string[]
const numberResult = processInput(123);     // number[]

// Real-world example: API client
interface ApiClient {
    get(url: string): Promise<Response>;
    post(url: string, data: object): Promise<Response>;
    put(url: string, data: object): Promise<Response>;
    delete(url: string): Promise<Response>;
}
```

### this Parameter

```typescript
// Typing 'this' context
interface Clickable {
    onClick(this: Clickable): void;
}

const button: Clickable = {
    onClick() {
        console.log('Button clicked');
        // 'this' is properly typed as Clickable
    }
};

// Real-world example: Event handlers
class EventEmitter {
    private events: Map<string, Function[]> = new Map();

    on(event: string, callback: (this: EventEmitter, data: any) => void) {
        if (!this.events.has(event)) {
            this.events.set(event, []);
        }
        this.events.get(event)!.push(callback);
    }

    emit(event: string, data: any) {
        this.events.get(event)?.forEach(callback => callback.call(this, data));
    }
}
```

---

## Objects & Interfaces

### Interface Deep Dive

```typescript
// Basic interface
interface Person {
    readonly id: string;
    name: string;
    age: number;
    email?: string; // Optional property
}

// Interface with methods
interface Calculator {
    add(a: number, b: number): number;
    subtract(a: number, b: number): number;
    multiply?: (a: number, b: number) => number; // Optional method
}

// Interface extending multiple interfaces
interface Employee extends Person {
    employeeId: string;
    department: string;
    startDate: Date;
}

// Interface for function types
interface StringFormatter {
    (input: string): string;
}

const toUpper: StringFormatter = (input) => input.toUpperCase();
```

### Advanced Object Patterns

```typescript
// Readonly properties
interface Config {
    readonly apiKey: string;
    readonly baseUrl: string;
}

const config: Config = {
    apiKey: '12345',
    baseUrl: 'https://api.example.com'
};
// config.apiKey = 'new'; // Error - readonly

// Index signatures with constraints
interface Cache {
    [key: string]: any;
    maxSize: number; // Can have predefined properties too
}

// Real-world example: React component props
interface ButtonProps {
    children: React.ReactNode;
    onClick: (event: React.MouseEvent) => void;
    variant?: 'primary' | 'secondary' | 'danger';
    size?: 'small' | 'medium' | 'large';
    disabled?: boolean;
    className?: string;
}
```

---

## Classes & OOP

### Class Fundamentals

```typescript
class Person {
    // Properties
    public name: string;
    private age: number;
    protected email: string;
    readonly id: string;

    // Static properties
    static species = 'Homo sapiens';
    private static instanceCount = 0;

    constructor(name: string, age: number, email: string) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.id = Math.random().toString(36);
        Person.instanceCount++;
    }

    // Methods
    public greet(): string {
        return `Hello, my name is ${this.name}`;
    }

    // Private method
    private getBirthYear(): number {
        return new Date().getFullYear() - this.age;
    }

    // Static method
    static getInstanceCount(): number {
        return Person.instanceCount;
    }

    // Getter
    get isAdult(): boolean {
        return this.age >= 18;
    }

    // Setter
    set updateEmail(newEmail: string) {
        if (newEmail.includes('@')) {
            this.email = newEmail;
        } else {
            throw new Error('Invalid email address');
        }
    }
}
```

### Inheritance & Polymorphism

```typescript
// Base class
abstract class Animal {
    constructor(public name: string, protected age: number) {}

    // Abstract method - must be implemented by derived classes
    abstract makeSound(): string;

    // Concrete method
    sleep(): string {
        return `${this.name} is sleeping`;
    }
}

// Derived class
class Dog extends Animal {
    constructor(name: string, age: number, public breed: string) {
        super(name, age);
    }

    // Implement abstract method
    makeSound(): string {
        return 'Woof! Woof!';
    }

    // Override method
    sleep(): string {
        return `${this.name} the ${this.breed} is sleeping`;
    }

    // New method
    fetch(): string {
        return `${this.name} is fetching the ball`;
    }
}

// Real-world example: API service base class
abstract class ApiService {
    constructor(protected baseURL: string) {}

    protected async request<T>(
        endpoint: string, 
        options: RequestInit = {}
    ): Promise<T> {
        const response = await fetch(`${this.baseURL}${endpoint}`, {
            headers: {
                'Content-Type': 'application/json',
                ...options.headers,
            },
            ...options,
        });

        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }

        return response.json();
    }
}

class UserService extends ApiService {
    constructor() {
        super('https://api.example.com/users');
    }

    async getUser(id: string): Promise<User> {
        return this.request<User>(`/${id}`);
    }

    async createUser(userData: Omit<User, 'id'>): Promise<User> {
        return this.request<User>('', {
            method: 'POST',
            body: JSON.stringify(userData),
        });
    }
}
```

### Interfaces with Classes

```typescript
// Interface defining class structure
interface Serializable {
    serialize(): string;
    deserialize(data: string): void;
}

interface Identifiable {
    id: string;
}

// Class implementing multiple interfaces
class Product implements Serializable, Identifiable {
    constructor(
        public id: string,
        public name: string,
        public price: number
    ) {}

    serialize(): string {
        return JSON.stringify({
            id: this.id,
            name: this.name,
            price: this.price
        });
    }

    deserialize(data: string): void {
        const parsed = JSON.parse(data);
        this.id = parsed.id;
        this.name = parsed.name;
        this.price = parsed.price;
    }
}
```

---

## Generics

### Generic Functions & Interfaces

```typescript
// Generic function
function identity<T>(value: T): T {
    return value;
}

const result1 = identity<string>('hello'); // Explicit type
const result2 = identity(42);              // Type inference

// Generic constraints
interface HasLength {
    length: number;
}

function logLength<T extends HasLength>(item: T): void {
    console.log(item.length);
}

logLength('hello');    // 5
logLength([1, 2, 3]);  // 3
// logLength(42);      // Error - number doesn't have length

// Multiple generic parameters
function merge<T, U>(obj1: T, obj2: U): T & U {
    return { ...obj1, ...obj2 };
}

const merged = merge({ name: 'Alice' }, { age: 30 });
// { name: 'Alice', age: 30 }
```

### Generic Classes & Interfaces

```typescript
// Generic interface
interface Repository<T> {
    findById(id: string): Promise<T | null>;
    save(entity: T): Promise<T>;
    delete(id: string): Promise<void>;
}

// Generic class
class GenericRepository<T extends { id: string }> implements Repository<T> {
    private entities: T[] = [];

    async findById(id: string): Promise<T | null> {
        return this.entities.find(entity => entity.id === id) || null;
    }

    async save(entity: T): Promise<T> {
        const existingIndex = this.entities.findIndex(e => e.id === entity.id);
        if (existingIndex >= 0) {
            this.entities[existingIndex] = entity;
        } else {
            this.entities.push(entity);
        }
        return entity;
    }

    async delete(id: string): Promise<void> {
        this.entities = this.entities.filter(entity => entity.id !== id);
    }
}

// Real-world example: API response wrapper
interface ApiResponse<T> {
    data: T;
    status: number;
    message: string;
    timestamp: Date;
}

async function fetchUser(userId: string): Promise<ApiResponse<User>> {
    const response = await fetch(`/api/users/${userId}`);
    const data = await response.json();
    return {
        data,
        status: response.status,
        message: 'Success',
        timestamp: new Date()
    };
}
```

### Advanced Generic Patterns

```typescript
// Conditional types with generics
type Flatten<T> = T extends Array<infer U> ? U : T;

type StringArray = string[];
type StringType = Flatten<StringArray>; // string

type NumberType = Flatten<number>;      // number

// Generic constraints with keyof
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}

const user = { name: 'Alice', age: 30 };
const userName = getProperty(user, 'name');  // string
const userAge = getProperty(user, 'age');    // number
// const userEmail = getProperty(user, 'email'); // Error

// Mapped types with generics
type Optional<T> = {
    [P in keyof T]?: T[P];
};

type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

// Real-world example: Builder pattern
class QueryBuilder<T> {
    private filters: Partial<T> = {};
    private sortBy?: keyof T;
    private sortOrder: 'asc' | 'desc' = 'asc';

    where(filters: Partial<T>): this {
        this.filters = { ...this.filters, ...filters };
        return this;
    }

    orderBy(field: keyof T, order: 'asc' | 'desc' = 'asc'): this {
        this.sortBy = field;
        this.sortOrder = order;
        return this;
    }

    build(): string {
        let query = '';
        // Build query string from filters and sort options
        return query;
    }
}

interface UserFilters {
    name?: string;
    age?: number;
    email?: string;
}

const userQuery = new QueryBuilder<UserFilters>()
    .where({ age: 25 })
    .orderBy('name', 'asc')
    .build();
```

---

## Utility Types

### Built-in Utility Types

```typescript
interface User {
    id: string;
    name: string;
    email: string;
    age: number;
    createdAt: Date;
}

// Partial - all properties optional
type PartialUser = Partial<User>;

// Required - all properties required
type RequiredUser = Required<User>;

// Readonly - all properties readonly
type ReadonlyUser = Readonly<User>;

// Pick - select specific properties
type UserPreview = Pick<User, 'id' | 'name'>;

// Omit - exclude specific properties
type UserWithoutId = Omit<User, 'id'>;

// Record - object with specific key and value types
type UserMap = Record<string, User>;

// Extract - extract union members that are assignable to type
type StringKeys = Extract<keyof User, string>;

// Exclude - exclude union members that are assignable to type
type NonStringKeys = Exclude<keyof User, string>;

// ReturnType - get function return type
type UserPromise = ReturnType<typeof fetchUser>;

// Parameters - get function parameters as tuple
type FetchUserParams = Parameters<typeof fetchUser>;
```

### Custom Utility Types

```typescript
// Make specific properties optional
type Optional<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;

type UserWithOptionalEmail = Optional<User, 'email' | 'age'>;

// Make specific properties required
type Required<T, K extends keyof T> = T & Required<Pick<T, K>>;

// Deep partial
type DeepPartial<T> = {
    [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

// Deep readonly
type DeepReadonly<T> = {
    readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};

// Function that returns a class constructor
type Constructor<T = {}> = new (...args: any[]) => T;

// Mixin pattern
function Timestamped<TBase extends Constructor>(Base: TBase) {
    return class extends Base {
        timestamp = new Date();
    };
}

class User {
    name: string;
    
    constructor(name: string) {
        this.name = name;
    }
}

const TimestampedUser = Timestamped(User);
const user = new TimestampedUser('Alice');
console.log(user.timestamp); // Current date
```

---

## Real-world Patterns

### API Layer with Type Safety

```typescript
// API types
interface ApiSuccess<T> {
    success: true;
    data: T;
    timestamp: Date;
}

interface ApiError {
    success: false;
    error: {
        code: string;
        message: string;
        details?: unknown;
    };
    timestamp: Date;
}

type ApiResult<T> = ApiSuccess<T> | ApiError;

// API client with error handling
class HttpClient {
    private baseURL: string;

    constructor(baseURL: string) {
        this.baseURL = baseURL;
    }

    async request<T>(
        endpoint: string,
        options: RequestInit = {}
    ): Promise<ApiResult<T>> {
        try {
            const response = await fetch(`${this.baseURL}${endpoint}`, {
                headers: {
                    'Content-Type': 'application/json',
                    ...options.headers,
                },
                ...options,
            });

            if (!response.ok) {
                throw new Error(`HTTP ${response.status}: ${response.statusText}`);
            }

            const data = await response.json();

            return {
                success: true,
                data,
                timestamp: new Date(),
            };
        } catch (error) {
            return {
                success: false,
                error: {
                    code: 'FETCH_ERROR',
                    message: error instanceof Error ? error.message : 'Unknown error',
                    details: error,
                },
                timestamp: new Date(),
            };
        }
    }

    async get<T>(endpoint: string): Promise<ApiResult<T>> {
        return this.request<T>(endpoint);
    }

    async post<T>(endpoint: string, data: unknown): Promise<ApiResult<T>> {
        return this.request<T>(endpoint, {
            method: 'POST',
            body: JSON.stringify(data),
        });
    }
}

// Usage
interface Post {
    id: string;
    title: string;
    content: string;
    author: string;
}

const api = new HttpClient('https://api.example.com');

async function getPosts(): Promise<void> {
    const result = await api.get<Post[]>('/posts');
    
    if (result.success) {
        // TypeScript knows result.data is Post[]
        console.log('Posts:', result.data);
    } else {
        console.error('Error:', result.error.message);
    }
}
```

### State Management Pattern

```typescript
// Redux-like action types
type Action<T extends string, P = undefined> = P extends undefined
    ? { type: T }
    : { type: T; payload: P };

// User actions
type UserAction =
    | Action<'USER_LOADING'>
    | Action<'USER_LOADED', User>
    | Action<'USER_ERROR', string>
    | Action<'USER_UPDATE', Partial<User>>;

// State with discriminated union
type UserState =
    | { status: 'idle'; user: null; error: null }
    | { status: 'loading'; user: null; error: null }
    | { status: 'success'; user: User; error: null }
    | { status: 'error'; user: null; error: string };

// Reducer with exhaustive type checking
function userReducer(state: UserState, action: UserAction): UserState {
    switch (action.type) {
        case 'USER_LOADING':
            return { status: 'loading', user: null, error: null };
            
        case 'USER_LOADED':
            return { status: 'success', user: action.payload, error: null };
            
        case 'USER_ERROR':
            return { status: 'error', user: null, error: action.payload };
            
        case 'USER_UPDATE':
            if (state.status === 'success') {
                return {
                    ...state,
                    user: { ...state.user, ...action.payload },
                };
            }
            return state;
            
        default:
            // This ensures we handle all action types
            const _exhaustiveCheck: never = action;
            return state;
    }
}
```

### Configuration Pattern

```typescript
// Configuration with environment-specific values
interface DatabaseConfig {
    host: string;
    port: number;
    database: string;
    username: string;
    password: string;
}

interface ApiConfig {
    baseURL: string;
    timeout: number;
    retries: number;
}

interface AppConfig {
    environment: 'development' | 'staging' | 'production';
    database: DatabaseConfig;
    api: ApiConfig;
    features: {
        enableCache: boolean;
        enableLogging: boolean;
        maxUploadSize: number;
    };
}

// Configuration builder with validation
class ConfigBuilder {
    private config: Partial<AppConfig> = {};

    setEnvironment(env: AppConfig['environment']): this {
        this.config.environment = env;
        return this;
    }

    setDatabase(config: DatabaseConfig): this {
        this.config.database = config;
        return this;
    }

    setApi(config: ApiConfig): this {
        this.config.api = config;
        return this;
    }

    setFeatures(features: AppConfig['features']): this {
        this.config.features = features;
        return this;
    }

    build(): AppConfig {
        if (!this.config.environment || !this.config.database || !this.config.api) {
            throw new Error('Missing required configuration');
        }

        return this.config as AppConfig;
    }
}

// Usage
const config = new ConfigBuilder()
    .setEnvironment('development')
    .setDatabase({
        host: 'localhost',
        port: 5432,
        database: 'mydb',
        username: 'user',
        password: 'pass',
    })
    .setApi({
        baseURL: 'https://api.example.com',
        timeout: 5000,
        retries: 3,
    })
    .setFeatures({
        enableCache: true,
        enableLogging: true,
        maxUploadSize: 1024 * 1024 * 10, // 10MB
    })
    .build();
```

### Event System with Type Safety

```typescript
// Event map interface
interface EventMap {
    userCreated: { userId: string; email: string };
    userUpdated: { userId: string; changes: Partial<User> };
    orderPlaced: { orderId: string; amount: number; userId: string };
    paymentProcessed: { paymentId: string; status: 'success' | 'failed' };
}

// Type-safe event emitter
class TypedEventEmitter<TEvents extends Record<string, any>> {
    private listeners: {
        [K in keyof TEvents]?: Array<(data: TEvents[K]) => void>;
    } = {};

    on<K extends keyof TEvents>(
        event: K,
        listener: (data: TEvents[K]) => void
    ): void {
        if (!this.listeners[event]) {
            this.listeners[event] = [];
        }
        this.listeners[event]!.push(listener);
    }

    off<K extends keyof TEvents>(
        event: K,
        listener: (data: TEvents[K]) => void
    ): void {
        if (this.listeners[event]) {
            this.listeners[event] = this.listeners[event]!.filter(
                l => l !== listener
            );
        }
    }

    emit<K extends keyof TEvents>(event: K, data: TEvents[K]): void {
        this.listeners[event]?.forEach(listener => listener(data));
    }
}

// Usage
const eventBus = new TypedEventEmitter<EventMap>();

// Type-safe event listening
eventBus.on('userCreated', (data) => {
    // TypeScript knows data has userId and email
    console.log(`User ${data.userId} created with email ${data.email}`);
});

eventBus.on('orderPlaced', (data) => {
    // TypeScript knows data has orderId, amount, and userId
    console.log(`Order ${data.orderId} placed for $${data.amount}`);
});

// Type-safe event emitting
eventBus.emit('userCreated', {
    userId: '123',
    email: 'alice@example.com',
});

// This would cause a TypeScript error:
// eventBus.emit('userCreated', { userId: '123' }); // Missing email
```

This comprehensive guide covers TypeScript from basic setup to advanced patterns used in real-world applications. The key to mastering TypeScript is practice and gradually incorporating these patterns into your projects.
