# 🐾 Peluditos Backend API

<div align="center">

![Node.js](https://img.shields.io/badge/Node.js-43853D?style=for-the-badge&logo=node.js&logoColor=white)
![Express.js](https://img.shields.io/badge/Express.js-404D59?style=for-the-badge)
![MongoDB](https://img.shields.io/badge/MongoDB-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white)
![Clerk](https://img.shields.io/badge/Clerk-6C47FF?style=for-the-badge&logo=clerk&logoColor=white)
![Cloudinary](https://img.shields.io/badge/Cloudinary-3448C5?style=for-the-badge&logo=cloudinary&logoColor=white)
![Arcjet](https://img.shields.io/badge/Arcjet-FF6B6B?style=for-the-badge&logo=shield&logoColor=white)

</div>

---

## 📋 Descripción del Proyecto

**Peluditos Backend** es una API REST robusta y escalable diseñada para una red social centrada en mascotas. Esta aplicación permite a los amantes de los animales conectarse, compartir momentos especiales de sus mascotas, y construir una comunidad vibrante alrededor del amor por los animales.

### 🎯 Objetivos del Proyecto

- **Conectar** a propietarios de mascotas en una plataforma segura
- **Compartir** momentos especiales y cuidados de mascotas
- **Construir** una comunidad activa y participativa
- **Proporcionar** una experiencia de usuario fluida y moderna

---

## 🏗️ Arquitectura Técnica

### 📐 Patrón de Arquitectura

La aplicación sigue el patrón **MVC (Model-View-Controller)** con una arquitectura de capas bien definida:

```mermaid
graph TB
    subgraph "Frontend Layer"
        WEB[Web App]
        MOBILE[Mobile App]
    end

    subgraph "Security & Middleware"
        CORS[CORS Policy]
        AUTH[Clerk Authentication]
        ARCJET[Arcjet Security Shield]
        UPLOAD[Multer File Handler]
        VALIDATION[Request Validation]
    end

    subgraph "API Gateway"
        EXPRESS[Express.js Server]
        ROUTES[API Routes Router]
        ERROR[Error Handler]
    end

    subgraph "Business Logic Layer"
        USER_CTRL[User Controller]
        POST_CTRL[Post Controller]
        COMMENT_CTRL[Comment Controller]
        NOTIFICATION_CTRL[Notification Controller]
    end

    subgraph "Data Access Layer"
        MONGOOSE[Mongoose ODM]
        MODELS[Data Models]
    end

    subgraph "External Services"
        CLERK_API[Clerk Auth API]
        CLOUDINARY_API[Cloudinary CDN]
        MONGODB[MongoDB Atlas]
    end

    WEB --> CORS
    MOBILE --> CORS
    CORS --> AUTH
    AUTH --> ARCJET
    ARCJET --> VALIDATION
    VALIDATION --> UPLOAD
    UPLOAD --> EXPRESS
    EXPRESS --> ROUTES
    ROUTES --> ERROR
    ROUTES --> USER_CTRL
    ROUTES --> POST_CTRL
    ROUTES --> COMMENT_CTRL
    ROUTES --> NOTIFICATION_CTRL
    USER_CTRL --> MONGOOSE
    POST_CTRL --> MONGOOSE
    COMMENT_CTRL --> MONGOOSE
    NOTIFICATION_CTRL --> MONGOOSE
    MONGOOSE --> MODELS
    MODELS --> MONGODB
    AUTH --> CLERK_API
    UPLOAD --> CLOUDINARY_API
```

### 🔧 Stack Tecnológico

| Categoría | Tecnología | Propósito |
|-----------|------------|-----------|
| **Runtime** | Node.js 18+ | Entorno de ejecución JavaScript |
| **Framework** | Express.js | Framework web minimalista |
| **Base de Datos** | MongoDB Atlas | Base de datos NoSQL en la nube |
| **ODM** | Mongoose | Modelado de objetos para MongoDB |
| **Autenticación** | Clerk | Gestión completa de usuarios |
| **Almacenamiento** | Cloudinary | CDN para imágenes optimizadas |
| **Seguridad** | Arcjet | Protección contra ataques |
| **Deployment** | Vercel | Plataforma de despliegue serverless |

---

## 🏛️ Arquitectura de Software

### 📐 Patrón Arquitectónico Principal: **Clean Architecture**

Este proyecto implementa una **Clean Architecture** adaptada para Node.js, que proporciona separación de responsabilidades, testabilidad y mantenibilidad. La arquitectura está organizada en capas concéntricas donde las dependencias apuntan hacia el centro.

```mermaid
graph TB
    subgraph "External Layer (Frameworks & Drivers)"
        EXPRESS[Express.js Server]
        MONGO[MongoDB Database]
        CLERK_EXT[Clerk Auth Service]
        CLOUDINARY_EXT[Cloudinary CDN]
        ARCJET_EXT[Arcjet Security]
    end

    subgraph "Interface Adapters Layer"
        ROUTES[Routes]
        CONTROLLERS[Controllers]
        MIDDLEWARE[Middleware]
        MODELS[Mongoose Models]
    end

    subgraph "Application Business Rules Layer"
        USECASES[Use Cases]
        SERVICES[Business Services]
        VALIDATORS[Data Validators]
    end

    subgraph "Enterprise Business Rules Layer (Core)"
        ENTITIES[Domain Entities]
        REPOSITORIES[Repository Interfaces]
        BUSINESS_LOGIC[Business Logic]
    end

    EXPRESS --> ROUTES
    ROUTES --> CONTROLLERS
    CONTROLLERS --> USECASES
    USECASES --> ENTITIES
    CONTROLLERS --> SERVICES
    SERVICES --> REPOSITORIES
    MODELS --> MONGO
    MIDDLEWARE --> CLERK_EXT
    MIDDLEWARE --> ARCJET_EXT
    CONTROLLERS --> CLOUDINARY_EXT
```

### 🔄 Principios SOLID Implementados

#### **1. Single Responsibility Principle (SRP)**
Cada clase y módulo tiene una única razón para cambiar:

```javascript
// ✅ Correcto: Cada controlador maneja una entidad específica
class UserController {
  async getUserProfile(req, res) { /* Solo maneja perfiles de usuario */ }
  async updateProfile(req, res) { /* Solo actualiza perfiles */ }
}

class PostController {
  async createPost(req, res) { /* Solo maneja creación de posts */ }
  async getPosts(req, res) { /* Solo obtiene posts */ }
}
```

#### **2. Open/Closed Principle (OCP)**
El sistema está abierto para extensión pero cerrado para modificación:

```javascript
// ✅ Base abstracta para controladores
class BaseController {
  constructor(service) {
    this.service = service;
  }

  async handleRequest(req, res, operation) {
    try {
      const result = await operation();
      res.status(200).json(result);
    } catch (error) {
      this.handleError(res, error);
    }
  }
}

// Extensión sin modificar la base
class UserController extends BaseController {
  constructor(userService) {
    super(userService);
  }
  // Métodos específicos de usuario
}
```

#### **3. Liskov Substitution Principle (LSP)**
Los objetos derivados pueden sustituir a sus clases base:

```javascript
// ✅ Interface común para notificaciones
class NotificationService {
  async send(notification) {
    throw new Error('Method must be implemented');
  }
}

class EmailNotificationService extends NotificationService {
  async send(notification) {
    // Implementación específica para email
  }
}

class PushNotificationService extends NotificationService {
  async send(notification) {
    // Implementación específica para push
  }
}
```

#### **4. Interface Segregation Principle (ISP)**
Interfaces específicas en lugar de una general:

```javascript
// ✅ Interfaces segregadas
class UserReadService {
  async findById(id) { /* Solo lectura */ }
  async findByUsername(username) { /* Solo lectura */ }
}

class UserWriteService {
  async create(userData) { /* Solo escritura */ }
  async update(id, userData) { /* Solo escritura */ }
}
```

#### **5. Dependency Inversion Principle (DIP)**
Dependencias de abstracciones, no de concreciones:

```javascript
// ✅ Inyección de dependencias
class PostController {
  constructor(postService, notificationService, uploadService) {
    this.postService = postService;
    this.notificationService = notificationService;
    this.uploadService = uploadService;
  }

  async createPost(req, res) {
    const imageUrl = await this.uploadService.upload(req.file);
    const post = await this.postService.create({...req.body, imageUrl});
    await this.notificationService.notifyFollowers(post);
    res.json(post);
  }
}
```

### 🏗️ Capas Arquitectónicas Detalladas

#### **1. Capa de Presentación (Presentation Layer)**

```javascript
// routes/user.route.js - Definición de endpoints
router.get('/profile/:username', getUserProfile);
router.post('/sync', protectRoute, syncUser);
router.put('/profile', protectRoute, updateProfile);

// middleware/auth.middleware.js - Lógica de autenticación
export const protectRoute = async (req, res, next) => {
  if (!req.auth().isAuthenticated) {
    return res.status(401).json({ message: "Unauthorized" });
  }
  next();
};
```

#### **2. Capa de Aplicación (Application Layer)**

```javascript
// controllers/user.controller.js - Orquestación de casos de uso
export const updateProfile = asyncHandler(async (req, res) => {
  const { userId } = getAuth(req);
  
  // Validación de entrada
  const validatedData = validateUserUpdate(req.body);
  
  // Caso de uso: actualizar perfil
  const user = await UserService.updateProfile(userId, validatedData);
  
  // Respuesta
  res.status(200).json({ user });
});
```

#### **3. Capa de Dominio (Domain Layer)**

```javascript
// models/user.model.js - Entidades de dominio con reglas de negocio
const userSchema = new mongoose.Schema({
  username: {
    type: String,
    required: true,
    unique: true,
    validate: {
      validator: function(v) {
        return /^[a-zA-Z0-9_]+$/.test(v); // Regla de negocio
      },
      message: 'Username solo puede contener letras, números y guiones bajos'
    }
  },
  // ...otros campos con validaciones de dominio
});

// Métodos de dominio
userSchema.methods.canFollow = function(targetUser) {
  return this._id.toString() !== targetUser._id.toString();
};

userSchema.methods.isFollowing = function(targetUserId) {
  return this.following.includes(targetUserId);
};
```

#### **4. Capa de Infraestructura (Infrastructure Layer)**

```javascript
// config/db.js - Acceso a datos
export const connectDB = async () => {
  try {
    await mongoose.connect(ENV.MONGO_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    });
    console.log("Connected to DB SUCCESSFULLY ✅");
  } catch (error) {
    console.log("Error connecting to MONGODB");
    process.exit(1);
  }
};

// config/cloudinary.js - Servicios externos
cloudinary.config({
  cloud_name: ENV.CLOUDINARY_CLOUD_NAME,
  api_key: ENV.CLOUDINARY_API_KEY,
  api_secret: ENV.CLOUDINARY_API_SECRET,
});
```

### 🎯 Patrones de Diseño Implementados

#### **1. Repository Pattern**
Abstracción del acceso a datos:

```javascript
class UserRepository {
  async findById(id) {
    return await User.findById(id).populate('followers following');
  }

  async findByUsername(username) {
    return await User.findOne({ username });
  }

  async create(userData) {
    return await User.create(userData);
  }

  async update(id, updateData) {
    return await User.findByIdAndUpdate(id, updateData, { new: true });
  }
}
```

#### **2. Service Layer Pattern**
Encapsulación de lógica de negocio:

```javascript
class PostService {
  constructor(postRepository, notificationService) {
    this.postRepository = postRepository;
    this.notificationService = notificationService;
  }

  async createPost(userId, postData) {
    // Validaciones de negocio
    if (!postData.content && !postData.image) {
      throw new Error('Post debe tener contenido o imagen');
    }

    // Crear post
    const post = await this.postRepository.create({
      user: userId,
      ...postData
    });

    // Notificar a seguidores
    await this.notificationService.notifyFollowers(userId, post);

    return post;
  }
}
```

#### **3. Factory Pattern**
Creación de objetos complejos:

```javascript
class NotificationFactory {
  static create(type, data) {
    switch (type) {
      case 'follow':
        return new FollowNotification(data);
      case 'like_post':
        return new LikePostNotification(data);
      case 'comment':
        return new CommentNotification(data);
      default:
        throw new Error(`Tipo de notificación no soportado: ${type}`);
    }
  }
}
```

#### **4. Middleware Pattern**
Cadena de responsabilidades:

```javascript
// Cadena de middleware
app.use(cors());                    // CORS handling
app.use(express.json());           // JSON parsing
app.use(clerkMiddleware());        // Authentication
app.use(arcjetMiddleware);         // Security
app.use('/api/users', userRoutes); // Routing
```

#### **5. Observer Pattern**
Sistema de eventos:

```javascript
class PostEventEmitter extends EventEmitter {
  constructor() {
    super();
    this.on('post:created', this.handlePostCreated);
    this.on('post:liked', this.handlePostLiked);
  }

  async handlePostCreated(post) {
    // Notificar seguidores
    await NotificationService.notifyFollowers(post.user, post);
  }

  async handlePostLiked(postId, userId) {
    // Crear notificación de like
    await NotificationService.createLikeNotification(postId, userId);
  }
}
```

### 🔄 Flujo de Datos en Clean Architecture

```mermaid
sequenceDiagram
    participant Route as Routes Layer
    participant Controller as Controllers Layer
    participant UseCase as Use Cases Layer
    participant Entity as Entities Layer
    participant Repo as Repository Layer
    participant DB as Database Layer

    Route->>Controller: HTTP Request
    Controller->>UseCase: Execute Business Logic
    UseCase->>Entity: Validate Business Rules
    Entity-->>UseCase: Validation Result
    UseCase->>Repo: Data Operation
    Repo->>DB: Database Query
    DB-->>Repo: Raw Data
    Repo-->>UseCase: Domain Objects
    UseCase-->>Controller: Business Result
    Controller-->>Route: HTTP Response
```

### 📊 Ventajas de esta Arquitectura

#### **✅ Beneficios Implementados**

1. **Testabilidad**: Cada capa puede ser testeada independientemente
2. **Mantenibilidad**: Cambios en una capa no afectan otras
3. **Escalabilidad**: Fácil añadir nuevas funcionalidades
4. **Flexibilidad**: Cambiar proveedores externos sin afectar lógica de negocio
5. **Reutilización**: Componentes pueden ser reutilizados en otros contextos

#### **🎯 Casos de Uso Ejemplo**

```javascript
// Caso de Uso: Crear Post con Imagen
class CreatePostUseCase {
  constructor(postRepository, imageService, notificationService) {
    this.postRepository = postRepository;
    this.imageService = imageService;
    this.notificationService = notificationService;
  }

  async execute(userId, postData, imageFile) {
    // 1. Validar entrada
    this.validateInput(postData);

    // 2. Procesar imagen si existe
    let imageUrl = null;
    if (imageFile) {
      imageUrl = await this.imageService.upload(imageFile);
    }

    // 3. Crear entidad de dominio
    const postEntity = new PostEntity({
      user: userId,
      content: postData.content,
      image: imageUrl
    });

    // 4. Validar reglas de negocio
    postEntity.validate();

    // 5. Persistir en repositorio
    const savedPost = await this.postRepository.save(postEntity);

    // 6. Efectos secundarios
    await this.notificationService.notifyFollowers(userId, savedPost);

    return savedPost;
  }
}
```

### 🔧 Configuración por Capas

#### **Capa de Configuración**
```javascript
// config/index.js - Configuración centralizada
export const config = {
  server: {
    port: ENV.PORT || 5000,
    environment: ENV.NODE_ENV || 'development'
  },
  database: {
    uri: ENV.MONGO_URI,
    options: {
      useNewUrlParser: true,
      useUnifiedTopology: true
    }
  },
  security: {
    jwt: {
      secret: ENV.JWT_SECRET,
      expiresIn: '24h'
    },
    cors: {
      origin: ENV.CORS_ORIGIN?.split(',') || ['http://localhost:3000']
    }
  }
};
```

---

## 🚀 Características Principales

### ✨ Funcionalidades Core

- **🔐 Autenticación Robusta**: Sistema de autenticación completo con Clerk
- **👥 Gestión de Perfiles**: Perfiles personalizables con biografías y ubicación
- **📱 Sistema de Seguimiento**: Funcionalidad de seguir/dejar de seguir usuarios
- **📝 Publicaciones Multimedia**: Posts con texto e imágenes optimizadas
- **💬 Sistema de Comentarios**: Comentarios interactivos en tiempo real
- **❤️ Interacciones Sociales**: Sistema de likes para posts y comentarios
- **🔔 Notificaciones Inteligentes**: Notificaciones contextuales y relevantes
- **🛡️ Seguridad Avanzada**: Protección multicapa contra amenazas
- **☁️ Gestión de Medios**: Subida y optimización automática de imágenes

### 🔒 Características de Seguridad

- **Rate Limiting**: Protección contra spam y ataques de fuerza bruta
- **CORS Security**: Configuración estricta de orígenes permitidos
- **Input Sanitization**: Validación y limpieza de datos de entrada
- **JWT Validation**: Verificación rigurosa de tokens de autenticación
- **Error Masking**: Ocultación de información sensible en errores

---

## 🗂️ Estructura Detallada del Proyecto

```
peluditos/
└── server/                          # 🏠 Directorio raíz del backend
    ├── 📄 package.json              # Dependencias y scripts npm
    ├── 📄 vercel.json               # Configuración de despliegue Vercel
    ├── 📄 .env.example              # Plantilla de variables de entorno
    ├── 📄 .gitignore                # Archivos ignorados por Git
    ├── 📄 README.md                 # Documentación del proyecto
    └── src/                         # 📁 Código fuente principal
        ├── 🚀 server.js             # Punto de entrada y configuración del servidor
        │
        ├── config/                  # ⚙️ Configuraciones del sistema
        │   ├── 🔧 env.js            # Validación y carga de variables de entorno
        │   ├── 🗄️ db.js             # Configuración y conexión a MongoDB
        │   ├── ☁️ cloudinary.js     # Configuración del servicio Cloudinary
        │   └── 🛡️ arcjet.js         # Configuración de seguridad Arcjet
        │
        ├── controllers/             # 🎮 Lógica de negocio y controladores
        │   ├── 👤 user.controller.js      # Gestión de usuarios y perfiles
        │   ├── 📝 post.controller.js      # Manejo de publicaciones
        │   ├── 💬 comment.controller.js   # Sistema de comentarios
        │   └── 🔔 notification.controller.js # Gestión de notificaciones
        │
        ├── middleware/              # 🛠️ Middlewares personalizados
        │   ├── 🔐 auth.middleware.js      # Verificación de autenticación
        │   ├── 🛡️ arcjet.middleware.js   # Middleware de seguridad
        │   └── 📤 upload.middleware.js    # Manejo de subida de archivos
        │
        ├── models/                  # 📊 Esquemas de datos (Mongoose)
        │   ├── 👤 user.model.js           # Modelo de usuario
        │   ├── 📝 post.model.js           # Modelo de publicación
        │   ├── 💬 comment.model.js        # Modelo de comentario
        │   └── 🔔 notification.model.js   # Modelo de notificación
        │
        └── routes/                  # 🛣️ Definición de rutas API
            ├── 👤 user.route.js           # Rutas de usuario
            ├── 📝 post.route.js           # Rutas de publicaciones
            ├── 💬 comment.route.js        # Rutas de comentarios
            └── 🔔 notification.route.js   # Rutas de notificaciones
```

### 📋 Descripción de Componentes

#### 🚀 server.js
**Punto de entrada principal** que configura el servidor Express, middlewares globales, rutas y manejo de errores.

#### ⚙️ config/
- **env.js**: Valida y carga variables de entorno de forma segura
- **db.js**: Establece conexión con MongoDB Atlas con retry logic
- **cloudinary.js**: Configura el servicio de CDN para optimización de imágenes
- **arcjet.js**: Implementa reglas de seguridad y rate limiting

#### 🎮 controllers/
Contienen la lógica de negocio principal:
- **user.controller.js**: Manejo de perfiles, seguimiento, sincronización
- **post.controller.js**: CRUD de publicaciones, sistema de likes
- **comment.controller.js**: Sistema de comentarios con interacciones
- **notification.controller.js**: Gestión inteligente de notificaciones

#### 🛠️ middleware/
- **auth.middleware.js**: Verifica tokens JWT de Clerk
- **arcjet.middleware.js**: Aplica reglas de seguridad personalizadas
- **upload.middleware.js**: Procesa y valida archivos multimedia

#### 📊 models/
Esquemas de Mongoose que definen la estructura de datos y validaciones.

#### 🛣️ routes/
Definición de endpoints RESTful organizados por funcionalidad.

---

## 📊 Modelos de Datos Detallados

### 👤 User Model - Esquema Completo

```javascript
const userSchema = new mongoose.Schema({
  clerkId: {
    type: String,
    required: true,
    unique: true,
    index: true              // Índice para búsquedas rápidas
  },
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,         // Normalización automática
    trim: true
  },
  firstName: {
    type: String,
    required: true,
    trim: true,
    maxlength: 50
  },
  lastName: {
    type: String,
    required: true,
    trim: true,
    maxlength: 50
  },
  username: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,
    trim: true,
    minlength: 3,
    maxlength: 20,
    match: /^[a-zA-Z0-9_]+$/  // Solo alfanuméricos y guiones bajos
  },
  profilePicture: {
    type: String,
    default: '',
    validate: {
      validator: function(v) {
        return !v || /^https?:\/\/.+/.test(v);  // URL válida o vacío
      },
      message: 'URL de imagen inválida'
    }
  },
  bannerImage: {
    type: String,
    default: ''
  },
  bio: {
    type: String,
    default: '',
    maxlength: 160           // Límite tipo Twitter
  },
  location: {
    type: String,
    default: '',
    maxlength: 100
  },
  followers: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User'
  }],
  following: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User'
  }],
  isVerified: {
    type: Boolean,
    default: false           // Para futuras verificaciones
  },
  isPrivate: {
    type: Boolean,
    default: false           // Perfiles privados
  }
}, {
  timestamps: true,          // createdAt y updatedAt automáticos
  toJSON: { virtuals: true },
  toObject: { virtuals: true }
});

// Campos virtuales para contadores
userSchema.virtual('followersCount').get(function() {
  return this.followers.length;
});

userSchema.virtual('followingCount').get(function() {
  return this.following.length;
});

// Índices compuestos para optimización
userSchema.index({ username: 1, email: 1 });
userSchema.index({ createdAt: -1 });
```

### 📝 Post Model - Esquema Avanzado

```javascript
const postSchema = new mongoose.Schema({
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true,
    index: true
  },
  content: {
    type: String,
    required: true,
    trim: true,
    maxlength: 280           // Límite de caracteres
  },
  image: {
    type: String,
    default: '',
    validate: {
      validator: function(v) {
        return !v || /^https?:\/\/.+/.test(v);
      },
      message: 'URL de imagen inválida'
    }
  },
  likes: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User'
  }],
  comments: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Comment'
  }],
  tags: [{
    type: String,
    lowercase: true,
    trim: true
  }],
  isDeleted: {
    type: Boolean,
    default: false           // Soft delete
  },
  visibility: {
    type: String,
    enum: ['public', 'followers', 'private'],
    default: 'public'
  }
}, {
  timestamps: true,
  toJSON: { virtuals: true }
});

// Campos virtuales
postSchema.virtual('likesCount').get(function() {
  return this.likes.length;
});

postSchema.virtual('commentsCount').get(function() {
  return this.comments.length;
});

// Middleware para actualizar contadores
postSchema.pre('save', function(next) {
  if (this.isModified('content')) {
    // Extraer hashtags automáticamente
    const hashtags = this.content.match(/#\w+/g);
    this.tags = hashtags ? hashtags.map(tag => tag.slice(1)) : [];
  }
  next();
});

// Índices para optimización
postSchema.index({ user: 1, createdAt: -1 });
postSchema.index({ tags: 1 });
postSchema.index({ createdAt: -1 });
```

### 💬 Comment Model - Sistema Avanzado

```javascript
const commentSchema = new mongoose.Schema({
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true
  },
  post: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Post',
    required: true,
    index: true
  },
  content: {
    type: String,
    required: true,
    trim: true,
    maxlength: 280
  },
  likes: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User'
  }],
  parentComment: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Comment',
    default: null            // Para comentarios anidados
  },
  replies: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Comment'
  }],
  isDeleted: {
    type: Boolean,
    default: false
  },
  editedAt: {
    type: Date,
    default: null
  }
}, {
  timestamps: true,
  toJSON: { virtuals: true }
});

// Campo virtual para conteo de likes
commentSchema.virtual('likesCount').get(function() {
  return this.likes.length;
});

// Índices para búsquedas eficientes
commentSchema.index({ post: 1, createdAt: -1 });
commentSchema.index({ user: 1 });
commentSchema.index({ parentComment: 1 });
```

### 🔔 Notification Model - Sistema Inteligente

```javascript
const notificationSchema = new mongoose.Schema({
  from: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true
  },
  to: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true,
    index: true
  },
  type: {
    type: String,
    required: true,
    enum: [
      'follow',              // Usuario te siguió
      'like_post',           // Like en tu post
      'like_comment',        // Like en tu comentario
      'comment',             // Comentario en tu post
      'reply',               // Respuesta a tu comentario
      'mention'              // Mención en post/comentario
    ]
  },
  post: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Post',
    default: null
  },
  comment: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Comment',
    default: null
  },
  isRead: {
    type: Boolean,
    default: false,
    index: true
  },
  readAt: {
    type: Date,
    default: null
  }
}, {
  timestamps: true
});

// Índices compuestos para consultas eficientes
notificationSchema.index({ to: 1, isRead: 1, createdAt: -1 });
notificationSchema.index({ to: 1, type: 1 });

// Middleware para evitar notificaciones duplicadas
notificationSchema.pre('save', async function(next) {
  if (this.isNew) {
    const duplicate = await this.constructor.findOne({
      from: this.from,
      to: this.to,
      type: this.type,
      post: this.post,
      comment: this.comment,
      createdAt: { $gte: new Date(Date.now() - 5 * 60 * 1000) } // Últimos 5 minutos
    });
    
    if (duplicate) {
      return next(new Error('Notification already exists'));
    }
  }
  next();
});
```

---

## 🛣️ API Endpoints Completos

### 👤 Gestión de Usuarios

| Método | Endpoint | Descripción | Auth | Parámetros | Respuesta |
|--------|----------|-------------|------|------------|-----------|
| `GET` | `/api/users/profile/:username` | Obtener perfil público | ❌ | `username` | Usuario + estadísticas |
| `POST` | `/api/users/sync` | Sincronizar con Clerk | ✅ | Body: datos Clerk | Usuario sincronizado |
| `GET` | `/api/users/me` | Perfil del usuario actual | ✅ | - | Usuario completo |
| `PUT` | `/api/users/profile` | Actualizar perfil | ✅ | Body: datos perfil | Usuario actualizado |
| `POST` | `/api/users/follow/:targetUserId` | Seguir/Dejar seguir | ✅ | `targetUserId` | Estado seguimiento |
| `GET` | `/api/users/:userId/followers` | Lista de seguidores | ❌ | `userId` | Array usuarios |
| `GET` | `/api/users/:userId/following` | Lista de seguidos | ❌ | `userId` | Array usuarios |
| `GET` | `/api/users/search` | Buscar usuarios | ✅ | Query: `q` | Resultados búsqueda |

### 📝 Gestión de Posts

| Método | Endpoint | Descripción | Auth | Parámetros | Respuesta |
|--------|----------|-------------|------|------------|-----------|
| `GET` | `/api/posts` | Feed personalizado | ✅ | Query: `page`, `limit` | Posts paginados |
| `POST` | `/api/posts` | Crear publicación | ✅ | Body + imagen | Post creado |
| `GET` | `/api/posts/:id` | Obtener post específico | ❌ | `id` | Post + comentarios |
| `PUT` | `/api/posts/:id` | Editar post propio | ✅ | `id` + Body | Post actualizado |
| `DELETE` | `/api/posts/:id` | Eliminar post propio | ✅ | `id` | Confirmación |
| `POST` | `/api/posts/:id/like` | Toggle like | ✅ | `id` | Estado like |
| `GET` | `/api/posts/user/:userId` | Posts de usuario | ❌ | `userId` | Posts usuario |
| `GET` | `/api/posts/trending` | Posts populares | ❌ | Query: `timeframe` | Posts trending |

### 💬 Sistema de Comentarios

| Método | Endpoint | Descripción | Auth | Parámetros | Respuesta |
|--------|----------|-------------|------|------------|-----------|
| `GET` | `/api/comments/:postId` | Comentarios del post | ❌ | `postId` | Array comentarios |
| `POST` | `/api/comments/:postId` | Crear comentario | ✅ | `postId` + Body | Comentario creado |
| `PUT` | `/api/comments/:id` | Editar comentario | ✅ | `id` + Body | Comentario editado |
| `DELETE` | `/api/comments/:id` | Eliminar comentario | ✅ | `id` | Confirmación |
| `POST` | `/api/comments/:id/like` | Toggle like comentario | ✅ | `id` | Estado like |
| `POST` | `/api/comments/:id/reply` | Responder comentario | ✅ | `id` + Body | Respuesta creada |

### 🔔 Sistema de Notificaciones

| Método | Endpoint | Descripción | Auth | Parámetros | Respuesta |
|--------|----------|-------------|------|------------|-----------|
| `GET` | `/api/notifications` | Obtener notificaciones | ✅ | Query: `unread` | Array notificaciones |
| `PUT` | `/api/notifications/:id/read` | Marcar como leída | ✅ | `id` | Notificación actualizada |
| `DELETE` | `/api/notifications/:id` | Eliminar notificación | ✅ | `id` | Confirmación |
| `PUT` | `/api/notifications/read-all` | Marcar todas leídas | ✅ | - | Confirmación |
| `GET` | `/api/notifications/count` | Contador no leídas | ✅ | - | Número |

---

## 🔄 Flujos de Datos Críticos

### 📤 Proceso de Creación de Post

```mermaid
sequenceDiagram
    participant U as Usuario
    participant F as Frontend
    participant A as API Gateway
    participant Auth as Clerk Auth
    participant M as Middleware
    participant C as Controller
    participant DB as MongoDB
    participant CDN as Cloudinary
    participant N as Notification Service

    U->>F: Crear post con imagen
    F->>A: POST /api/posts + FormData
    A->>Auth: Verificar JWT token
    Auth-->>A: Token válido + userInfo
    A->>M: Procesar upload imagen
    M->>CDN: Subir imagen optimizada
    CDN-->>M: URL imagen + metadata
    M->>C: Procesar datos del post
    C->>DB: Guardar post en BD
    DB-->>C: Post creado con ID
    C->>N: Crear notificaciones seguidores
    N->>DB: Guardar notificaciones masivas
    C-->>A: Respuesta con post completo
    A-->>F: Post creado exitosamente
    F-->>U: Mostrar post en feed
```

### 🔔 Sistema de Notificaciones Inteligentes

```mermaid
sequenceDiagram
    participant U1 as Usuario A
    participant U2 as Usuario B
    participant API as API Server
    participant DB as MongoDB
    participant Queue as Notification Queue

    Note over U1,Queue: Flujo de Like en Post

    U1->>API: POST /api/posts/:id/like
    API->>DB: Toggle like en post
    DB-->>API: Like agregado
    
    alt Es nuevo like
        API->>Queue: Encolar notificación
        Queue->>DB: Crear notificación
        Note over DB: type: 'like_post'<br/>from: U1<br/>to: autor_post
    end
    
    API-->>U1: Respuesta actualizada
    
    Note over U2: Usuario B consulta notificaciones
    U2->>API: GET /api/notifications
    API->>DB: Buscar notificaciones no leídas
    DB-->>API: Lista notificaciones
    API-->>U2: Notificaciones + metadata
```

### 🔐 Flujo de Autenticación Segura

```mermaid
sequenceDiagram
    participant C as Cliente
    participant MW as Auth Middleware
    participant CLERK as Clerk Service
    participant DB as User Database
    participant API as API Controller

    C->>MW: Request con Authorization header
    MW->>MW: Extraer Bearer token
    MW->>CLERK: Verificar JWT con Clerk
    
    alt Token válido
        CLERK-->>MW: Usuario verificado + claims
        MW->>DB: Buscar usuario por clerkId
        
        alt Usuario existe
            DB-->>MW: Datos usuario completos
        else Usuario no existe
            MW->>API: Trigger sincronización
            API->>DB: Crear usuario desde Clerk
            DB-->>MW: Usuario creado
        end
        
        MW->>MW: Attachar usuario a request
        MW-->>API: Continuar con request autorizado
    else Token inválido
        CLERK-->>MW: Error verificación
        MW-->>C: 401 Unauthorized
    end
```

---

## 🛡️ Seguridad Multicapa

### 🔐 Autenticación y Autorización

#### Clerk Integration
- **JWT Verification**: Validación automática de tokens en cada request
- **User Sync**: Sincronización bidireccional con el servicio de Clerk
- **Session Management**: Manejo seguro de sesiones de usuario
- **Role-based Access**: Sistema de roles y permisos (futuro)

```javascript
// Ejemplo de middleware de autenticación
const authMiddleware = async (req, res, next) => {
  try {
    const token = req.headers.authorization?.replace('Bearer ', '');
    if (!token) return res.status(401).json({ error: 'Token requerido' });

    const decoded = await verifyToken(token);
    const user = await User.findOne({ clerkId: decoded.sub });
    
    if (!user) {
      // Auto-sync si el usuario no existe
      const clerkUser = await clerkClient.users.getUser(decoded.sub);
      user = await createUserFromClerk(clerkUser);
    }

    req.user = user;
    next();
  } catch (error) {
    res.status(401).json({ error: 'Token inválido' });
  }
};
```

### 🛡️ Arcjet Security Shield

#### Protecciones Implementadas

```javascript
// Configuración de Arcjet
const aj = arcjet({
  key: process.env.ARCJET_KEY,
  rules: [
    // Rate limiting por IP
    rateLimit({
      mode: 'LIVE',
      characteristics: ['ip'],
      window: '1m',
      max: 60
    }),
    
    // Rate limiting por usuario autenticado
    rateLimit({
      mode: 'LIVE',
      characteristics: ['userId'],
      window: '1m',
      max: 100
    }),
    
    // Protección contra bots
    detectBot({
      mode: 'LIVE',
      block: ['AUTOMATED']
    }),
    
    // Protección de formularios
    shield({
      mode: 'LIVE'
    })
  ]
});
```

#### Métricas de Seguridad
- **Rate Limiting**: 60 requests/minuto por IP, 100/minuto por usuario
- **Bot Detection**: Bloqueo automático de tráfico automatizado
- **DDoS Protection**: Mitigación de ataques distribuidos
- **Request Validation**: Validación estricta de headers y payloads

### 🔒 Validación de Datos

```javascript
// Esquemas de validación con Joi
const postValidation = {
  content: Joi.string().min(1).max(280).required(),
  image: Joi.string().uri().optional(),
  tags: Joi.array().items(Joi.string().max(50)).max(5)
};

const userUpdateValidation = {
  firstName: Joi.string().min(2).max(50),
  lastName: Joi.string().min(2).max(50),
  bio: Joi.string().max(160),
  location: Joi.string().max(100)
};
```

### 🚨 Manejo de Errores

```javascript
// Sistema centralizado de manejo de errores
class AppError extends Error {
  constructor(message, statusCode, isOperational = true) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = isOperational;
    this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error';
    
    Error.captureStackTrace(this, this.constructor);
  }
}

// Middleware global de errores
const globalErrorHandler = (err, req, res, next) => {
  err.statusCode = err.statusCode || 500;
  err.status = err.status || 'error';

  if (process.env.NODE_ENV === 'production') {
    // En producción, no exponer stack traces
    res.status(err.statusCode).json({
      status: err.status,
      message: err.isOperational ? err.message : 'Algo salió mal'
    });
  } else {
    // En desarrollo, mostrar información completa
    res.status(err.statusCode).json({
      status: err.status,
      error: err,
      message: err.message,
      stack: err.stack
    });
  }
};
```

---

## 🚀 Instalación y Configuración

### 📋 Prerrequisitos del Sistema

- **Node.js**: Versión 18 o superior
- **MongoDB**: Atlas o instancia local
- **Git**: Para control de versiones
- **Cuentas de Servicios**:
  - Clerk (autenticación)
  - Cloudinary (CDN)
  - Arcjet (seguridad)

### 🔧 Variables de Entorno

Crear archivo `.env` en la raíz del proyecto:

```env
# === CONFIGURACIÓN DEL SERVIDOR ===
PORT=5000
NODE_ENV=development

# === BASE DE DATOS ===
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/peluditos

# === CLERK AUTHENTICATION ===
CLERK_PUBLISHABLE_KEY=pk_test_xxx...
CLERK_SECRET_KEY=sk_test_xxx...
CLERK_WEBHOOK_SECRET=whsec_xxx...

# === CLOUDINARY CDN ===
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=123456789012345
CLOUDINARY_API_SECRET=your_api_secret

# === ARCJET SECURITY ===
ARCJET_KEY=ajkey_xxx...

# === CONFIGURACIÓN ADICIONAL ===
CORS_ORIGIN=http://localhost:3000,https://yourdomain.com
MAX_FILE_SIZE=5242880  # 5MB en bytes
UPLOAD_ALLOWED_TYPES=jpg,jpeg,png,webp
```

### 📦 Proceso de Instalación

```bash
# 1. Clonar el repositorio
git clone https://github.com/felipesanchez-dev/Peluditos-App.git
cd Peluditos-App/server

# 2. Instalar dependencias
npm install

# 3. Copiar archivo de ambiente
cp .env.example .env
# Editar .env con tus credenciales

# 4. Verificar conexión a base de datos
npm run db:check

# 5. Ejecutar en modo desarrollo
npm run dev

# 6. Ejecutar en modo producción
npm run build
npm start
```

### 🔧 Scripts Disponibles

```json
{
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon src/server.js",
    "build": "echo 'Build completed'",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "lint": "eslint src/",
    "lint:fix": "eslint src/ --fix",
    "db:check": "node scripts/check-db-connection.js",
    "db:seed": "node scripts/seed-database.js"
  }
}
```

### 🌐 Despliegue en Vercel

#### Configuración automática:

```json
// vercel.json
{
  "version": 2,
  "builds": [
    {
      "src": "src/server.js",
      "use": "@vercel/node"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "src/server.js"
    }
  ],
  "env": {
    "NODE_ENV": "production"
  },
  "functions": {
    "src/server.js": {
      "maxDuration": 30
    }
  }
}
```

#### Comandos de despliegue:

```bash
# Instalar Vercel CLI
npm i -g vercel

# Login y configurar proyecto
vercel login
vercel

# Configurar variables de entorno
vercel env add MONGO_URI
vercel env add CLERK_SECRET_KEY
# ... resto de variables

# Desplegar a producción
vercel --prod
```

---

## 🧪 Testing y Calidad

### 📊 Cobertura de Tests

```bash
# Ejecutar suite completa de tests
npm test

# Tests con coverage
npm run test:coverage

# Tests en modo watch
npm run test:watch
```

### 🔍 Linting y Formateo

```bash
# Verificar código
npm run lint

# Corregir automáticamente
npm run lint:fix

# Formatear con Prettier
npm run format
```

### 📈 Monitoring y Logs

#### Logs Estructurados

```javascript
const logger = require('./utils/logger');

// Diferentes niveles de log
logger.info('Usuario autenticado', { userId, action: 'login' });
logger.warn('Rate limit alcanzado', { ip, endpoint });
logger.error('Error en base de datos', { error: error.message, stack: error.stack });
```

#### Health Checks

```javascript
// Endpoint de salud del sistema
app.get('/health', async (req, res) => {
  const health = {
    status: 'OK',
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
    services: {
      database: await checkDatabaseConnection(),
      cloudinary: await checkCloudinaryConnection(),
      clerk: await checkClerkConnection()
    }
  };
  
  res.status(200).json(health);
});
```

---

## 🔮 Roadmap y Futuras Mejoras

### 🎯 Versión 2.0 (Q1 2025)

- [ ] **WebSockets en Tiempo Real**
  - Notificaciones push instantáneas
  - Chat privado entre usuarios
  - Indicadores de presencia online

- [ ] **Sistema de Búsqueda Avanzada**
  - Búsqueda full-text con Elasticsearch
  - Filtros por ubicación, tipo de mascota
  - Sugerencias inteligentes

- [ ] **Análiticas y Métricas**
  - Dashboard de administración
  - Métricas de engagement
  - Reportes de uso

### 🚀 Versión 2.5 (Q2 2025)

- [ ] **Funcionalidades Premium**
  - Perfiles verificados
  - Funciones exclusivas para usuarios premium
  - Monetización con suscripciones

- [ ] **Inteligencia Artificial**
  - Moderación automática de contenido
  - Recomendaciones personalizadas
  - Reconocimiento de razas en imágenes

- [ ] **Aplicación Móvil Nativa**
  - React Native o Flutter
  - Notificaciones push nativas
  - Funcionalidades offline

### 🌟 Versión 3.0 (Q3 2025)

- [ ] **Ecosistema Expandido**
  - Marketplace de productos para mascotas
  - Directorio de veterinarios
  - Sistema de eventos y meetups

- [ ] **Integración IoT**
  - Conectividad con dispositivos de mascotas
  - Tracking de salud y actividad
  - Alertas automáticas

---

## 🤝 Contribución y Desarrollo

### 🔧 Guía para Contribuidores

#### 1. Configuración del Entorno

```bash
# Fork del repositorio
git clone https://github.com/tu-usuario/Peluditos-App.git
cd Peluditos-App/server

# Instalar dependencias
npm install

# Configurar pre-commit hooks
npm run prepare
```

#### 2. Estándares de Código

```javascript
// Convenciones de naming
const getUserProfile = async (userId) => {  // camelCase para funciones
  const user_data = await User.findById(userId);  // snake_case para variables locales
  return user_data;
};

// Estructura de archivos
// controllers/user.controller.js
exports.getUserProfile = async (req, res) => {
  try {
    // Lógica del controlador
  } catch (error) {
    // Manejo de errores
  }
};
```

#### 3. Commits Convencionales

```bash
# Tipos de commits
feat: nueva funcionalidad
fix: corrección de bug
docs: cambios en documentación
style: cambios de formato (no afectan lógica)
refactor: refactorización de código
test: agregar o modificar tests
chore: tareas de mantenimiento

# Ejemplos
git commit -m "feat: agregar sistema de comentarios anidados"
git commit -m "fix: corregir validación de email en registro"
git commit -m "docs: actualizar documentación de API"
```

#### 4. Process de Pull Request

1. **Crear rama feature**
   ```bash
   git checkout -b feature/nueva-caracteristica
   ```

2. **Desarrollar y testear**
   ```bash
   npm test
   npm run lint
   ```

3. **Commit y push**
   ```bash
   git commit -m "feat: descripción de la nueva característica"
   git push origin feature/nueva-caracteristica
   ```

4. **Crear Pull Request**
   - Descripción clara de los cambios
   - Referencias a issues relacionados
   - Screenshots si es aplicable

### 🏗️ Arquitectura para Contribuidores

```javascript
// Estructura recomendada para nuevos controllers
class BaseController {
  constructor(model) {
    this.model = model;
  }

  async create(req, res) {
    try {
      const item = await this.model.create(req.body);
      res.status(201).json({ success: true, data: item });
    } catch (error) {
      this.handleError(res, error);
    }
  }

  handleError(res, error) {
    if (error.name === 'ValidationError') {
      return res.status(400).json({ error: error.message });
    }
    res.status(500).json({ error: 'Error interno del servidor' });
  }
}
```

### 📚 Recursos para Desarrolladores

- **API Documentation**: Swagger UI disponible en `/api/docs`
- **Postman Collection**: Incluida en `/docs/postman/`
- **Database Schema**: Diagramas ER en `/docs/database/`
- **Architectural Decisions**: ADRs en `/docs/architecture/`

---

## 👨‍💻 Desarrollador

<div align="center">
  <img src="https://github.com/felipesanchez-dev.png" width="120" height="120" style="border-radius: 50%; border: 3px solid #16a34a;" alt="Felipe Reyes Sanchez" />
  
  **Felipe Reyes Sanchez**  
  *Full Stack Developer & UI/UX Enthusiast*
</div>

<div align="center">
  
[![Website](https://img.shields.io/badge/Website-felipesanchezdev.site-green?style=for-the-badge&logo=safari)](https://felipesanchezdev.site)
[![Email](https://img.shields.io/badge/Email-felipe@felipesanchezdev.site-red?style=for-the-badge&logo=gmail)](mailto:felipe@felipesanchezdev.site)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Felipe_Reyes_Sanchez-blue?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/felipereyessa)
[![GitHub](https://img.shields.io/badge/GitHub-@felipesanchez--dev-black?style=for-the-badge&logo=github)](https://github.com/felipesanchez-dev)
[![Instagram](https://img.shields.io/badge/Instagram-@felipesanchez__dev-purple?style=for-the-badge&logo=instagram)](https://www.instagram.com/felipesanchez_dev)
[![WhatsApp](https://img.shields.io/badge/WhatsApp-+573102_452542-green?style=for-the-badge&logo=whatsapp)](https://wa.me/573102452542)

</div>

---

### 📊 Estadísticas del Proyecto

<div align="center">

![GitHub Stars](https://img.shields.io/github/stars/felipesanchez-dev/Peluditos-App.git)
![GitHub Forks](https://img.shields.io/github/forks/felipesanchez-dev/Peluditos-App.git?style=social)
![GitHub Issues](https://img.shields.io/github/issues/felipesanchez-dev/Peluditos-App.git)
![GitHub Pull Requests](https://img.shields.io/github/issues-pr/felipesanchez-dev/Peluditos-App.git)
![Lines of Code](https://img.shields.io/tokei/lines/github/felipesanchez-dev/Peluditos-App.git)

</div>

### 🏆 Logros Técnicos v1.2

<div align="center">

![Accessibility](https://img.shields.io/badge/Accessibility-AA-green?style=flat&logo=accessibility)
![Security](https://img.shields.io/badge/Security-A+-green?style=flat&logo=security)
![API REST](https://img.shields.io/badge/API_REST-Optimized-blue?style=flat&logo=api)

</div>

---

<div align="center">
  <p><strong>Desarrollado por Felipe Reyes Sanchez</strong></p>
  
  <br>
  
  <img src="https://img.shields.io/badge/Made_with-Love_and_Code-red?style=for-the-badge" alt="Made with Love and Code" />
  <img src="https://img.shields.io/badge/Designed_for-Future_of_AI-green?style=for-the-badge" alt="Designed for Future of AI" />
  
  <br><br>
  
  **⭐ Si te gusta este proyecto, dale una estrella en GitHub ⭐**
  
</div>