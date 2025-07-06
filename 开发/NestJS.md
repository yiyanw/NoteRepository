# NestJS

# Introduction

* install Nest-CLI - `npm install -g @nestjs/cli`

* initialize new project - `nest new <projectName>`

* locally run project - `pnpm run start:dev`

* structure

  * support monorepo - one project contains multiple sub projects

  * file structure

    * ```
      root
      |-src - main code
      	|-app.controller.ts
      	|-app.controller.spec.ts
      	|-app.module.ts - default module AppModule
      	|-app.service.ts
      	|-main.ts - starting point
      |-test - test code
      |-package.json
      |-.eslintrc.js
      |-.gitignore
      |-.prettierrc
      |-nest-cli.json
      |-pnpm-lock.yaml
      |-tsconfig.build.json
      |-tsconfig.json
      ```

# Controllers, Routing, Requests

* controllers

  * ```ts
    // *.controller.ts
    
    import { Controller, Get } from '@nestjs/common';
    import { AppService } from './app.service';
    
    @Controller({ // it also can directly receive string as path @Controller('/xxx'), default is '/'
      path: '/',
    })
    export class AppController {
      constructor(private readonly appService: AppService) {}
    
      @Get()
      getHello(): string {
        return this.appService.getHello();
      }
    
      @Get('/bye') // can define sub path
      getBye(): string {
        return 'Bye!';
      }
      
      // has different decorators, get, post , put, ...
    }
    ```

* Resource controller

  * controller will try to match the url from top to buttom, only the first matched url will be invoked

  * CRUD of resource to Restful API

    * keep controllers thin and having at most 5 basic CRUD operations
  
      * build controllers around **resources**
      * resources do not always mean database table level, can be something more abstract
      * keep all controllers with the same naming pattern

    * create a controller
  
    * ```js
      @Controller('/<root_path>')
      export class <ControllerName> {
        @Get() // only match to '/<root_path>'
        findAll() {}
        @Get(':<paramName>') // only match to '/<root_path>/<param>'
        findOne(@Param('<paramName>') val: string) {
          return val;
        }
        @Post()
        create(@Body() input) {}
        @Patch(':<pName>')
        update(@Param() obj) {} // obj == { <pName>: <val> }
        @Delete(':<identity>')
      	@HttpCode(204) // usually not return things but response code 204 (no-content)
        remove(@Param('<identity>') id) {} 
      }
      ```

    * add this new controller into the module
  
      * ```ts
        @Module({
          imports: [],
          controllers: [<ControllerName>], // add controller here
          providers: [AppService],
        })
        ...
        ```

  * request payload - Data Transfer Objects (DTO)

    * for `Post` - easy to expect the content of input
  
      * ```ts
        // <DtoName1>.dto.ts file
        export class <DtoName1> { // usually name pattern is xxxDto
          name: string;
          description: string;
          when: string;
          address: string;
        }
        ```

      * then we can declare the input type with this dto

        * `<functionName>(@Body() obj: <DtoName>)`

  * update payload

    * for `Patch` - can only pass part of data to update
  
      * ```ts
        // <DtoName2>.dto.ts file
        export class <DtoName2> { // usually name pattern is xxxDto
          name?: string; // ? means optional feature
          description?: string;
          when?: string;
          address?: string;
        }
        ```

      * Alternative way is using `map-types`

        * install `map types` - `pnpm install --save @nestjs/mapped-types`
  
        * ```ts
          // <DtoName2>.dto.ts file
          import <DtoName1> from 'xxx'
          
          export class <DtoName2> extends PartialType(<DtoName1>) {
          	// all params in <DtoName1> will become optional
          }
          ```
  

# Database

* termology 

  * ORM - Object-Relational Mapping
    * Object-oriented abstraction for database schema

  * TypeOrm - ORM Library for Node.js

  * Nest.js TypeORM Module - Nest.js Module providing TypeORM integration

  * Entity - Database table mapped to a JavaScript/TypeScript class

  * Repository - Providers data access for a specific Entity

  * Query Builder - Allows building SQL queries in an object-oriented way

* Establish local DB

  * `docker-compose up`

* connecting to database

  * install `typeorm` - `npm install --save @nestjs/typeorm typeorm`

  * install database connector - `<mysql|pg>`

  * connection demo for `mysql`

    * ```ts
      // ***.module.ts
      
      import { Module } from '@nestjs/common';
      import { TypeOrmModule } from '@nestjs/typeorm';
      import { AppController } from './app.controller';
      import { AppService } from './app.service';
      import { EventsController } from './events.controller';
      
      @Module({
        imports: [TypeOrmModule.forRoot({
          type: 'mysql',
          host: '127.0.0.1',
          port: 3306,
          username: 'root',
          password: 'example',
          database: 'nest-events'
        })],
        controllers: [AppController, EventsController],
        providers: [AppService],
      })
      export class AppModule { }
      ```

* The Entity

  * ```ts
    // *.entity.ts file
    
    @Entity('<tableName>') // can specific some feature in Entity
    export class <entityName> {
      @PrimaryGeneratedColumn('uuid|rowid') // it will automatically generate value for id
      id: number;
      // @PrimaryColum() // it will not automatically generate value
      
      @Column('<type>', { <columnOptions> })
      name: string;
      @Column({ <columnOptions> })
      description: string;
      @Column()
      when: Date;
      @Column()
      address: string
      
      // need to decorate all features
    }
    ```

* how to use entities

  * ```ts
    // ***.module.ts
    
    @Module({
      imports: [...,
                TypeOrmModule.forFeature([<EntityName>]) // content that need to be injected
               ],
      ...
                
    })
    ```

  * ```ts
    // ***.controller.ts
    
    @Controller(...)
    export class <controllerName> {
      constructor(
      	@InjectRepository(<entityName>) // dependency injection
        private readonly repository: Repository<entityName>
      )
         
      @Get(...)
    	async findOne(...) {
        return await this.repository.findOne(...)
      }
                                             
      // common methods: find(), findOne(), save(), remove()
    }
    ```

  * conditions - [find](https://orkhan.gitbook.io/typeorm/docs/find-options)

    * ```ts
      this.repository.find({
        where: { <key>: <value> } // select * from <table> where <table>.<key> = <value>
      })
      
      this.repository.find({
        where: { <key>: Morethan(<value>) } // select * from <table> where <table>.<key> > <value>
      })
      
      this.repository.find({
        where: [{ <key>: <value> },{ <key2>: Like('regx')}], // inside {} is and, inside [], is or
        take: 2, // LIMIT 2
      	skip: XXX, // OFFSET XXX
        order: {
        	<key>: 'DESC'|'ASC' // order by <key> DESC|ASC
        }
      })
      ```

# Data Validation

* introduction to Pipes

  * Convert type in decorator [converters](https://docs.nestjs.com/pipes)

    * ```ts
      findOne(@Param('<keyName>', ParseIntPipe), key: number)
      ```

  * validation

    * ```ts 
      // ***.controller.ts
      create(@Body(ValidationPipe) input: <DtoName>) {...}
      
      // other way
      @UsePipes()
      create(input: <DtoName>){ ... }                                              
      
      
      // also can enable validation for body globally
      // main.ts
      import { ValidationPipe } from '@nestjs/common'
      bootstrap() {
        ...
        app.useGlobalPipes(new ValidationPipe());
        ...
      }
      ```

    * ```ts
      // ***.dto.ts
      import { Length } from 'class-validator';
      
      export class <DtoName> {
        @Length(5, 255, {message: 'The name length si wrong'}) // Length(min, max, errorMessage)
        <feature>: string
        ...
      }
      ```

      * more validation [here](https://docs.nestjs.com/pipes#class-validator)
      * need to install `class-validator` - `pnpm install --save class-validator`
      * need to install `class-validator` - `pnpm install --save class-transformer`

    * validation based on groups

      * ```ts
        @Length(5, 255, groups: ['<groupName>'])
        ```

      * ```ts
        @Body(new ValidationPipe({groups: ['<groupName>']}))
        ```

      * need to disable global `ValidationPipe`

# Modules, Providers, Dependency Injection

* creating module

  * `nest generate module <moduleName>`

* module is singleton pattern

* Standard VS Dynamic Modules - ????

* Provider

  * the independent unit that can be injected to other modules for providing some specific functionalities

    * easy to plugin
    * decoupling the project

  * ```ts
    @Module({
      ...,
      providers: [{
      	provide: AppService, // interface
      	useClass: AppChildService // implemented class
    	}, {
      	provide: 'APP_NAME', // also can inject primitive type, but need an extra decorator when injecting
      	useClass: 'Nest BackEnd'
    	}, {
      	// factory provider
      	provide: 'MESSAGE',
      	inject: [<DummyProvider>],
        useFactory: (app) => `${app.<dummyFunc>()} Factory.`
    }, <DummyProvider>
      ]
    })
    ```

  * ```ts
    ...
    @Injectable()
    export class <ClassName>{
      constructor(
      @Inject('APP_NAME') // need this for primitive type
      private readonly name: string
       
      @Inject('MESSAGE')
      private readonly message: string // message == `${app.<dummyFunc>()} Factory.`
      ) {...}
    ...
      }
    ```

# Configuration, Logging, and Errors

* Config different environments (different dbs, ips, ...)

* install config lib - `pnpm install --save @nestjs/config`

* access environmental variables - `process.env`

  * need to register ConfigMudule first

    * ```ts
      @Module({
        imports: [{
          ConfigModule.forRoot(),
          ...
        }],
        ...
      })
      ```

  * it will also read properties in `.env` file in root dir

    * ```.env
      DB_HOST=localhost
      DB_TYPE=mysql
      DB_PORT=3306
      DB_USER=root
      DB_PASSWORD=example
      DB_NAME=nest-events
      
      ...
      
      APP_URL=mywebsite.com
      SUPPORT_EMAIL=support@${APP_URL} // can re-use other property
      ```

* create configuration files

  * ```ts
    // src/config/orm.config.ts
    import { Event } from '../events/event.entity';
    import { TypeOrmModuleOptions } from '@nestjs/typeorm';
    import { registerAs } from '@nestjs/config';
    
    export default registerAs(
      'orm.config',
      (): TypeOrmModuleOptions => ({
        type: 'mysql',
        host: process.env.DB_HOST, // those values stored in .env file
        port: Number(process.env.DB_PORT),
        username: process.env.DB_USER,
        password: process.env.DB_PASSWORD,
        database: process.env.DB_NAME,
        entities: [Event],
        synchronize: true, // we can also have orm.config.prod.ts file to set this to false
      }),
    );
    ```

  * ```ts
    // ***.module.ts
    ...
    import ormConfig from './config/orm.config'; // for test env
    import ormConfigProd from './config/orm.config.prod'; // for prod env
    
    
    @Module({
      imports: [
        ConfigModule.forRoot({
          isGlobal: true,
          ignoreEnvFile: false, // enable it if we need read properties from other source, like docker
          envFilePath: '.env', // .env is default path
          load: [ormConfig], // ???
          expandVariables: true,
        }),
        TypeOrmModule.forRootAsync({ // change from forRoot to forRootAsync
          useFactory:
            process.env.NODE_ENV !== 'production' ? ormConfig : ormConfigProd,
        }),
      ...
      ]
      ...
        
    )
    ```

* logging

  * settings

    * ```ts
      // main.ts
      
      async function bootstrap() {
        const app = await NestFactory.create(AppModule, {
          logger: ['error', 'warn', 'debug'], // only enable error, warn and debug log
        });
        ...
      }
      ```

  * usage

    * ```ts
      export class EventsController {
        private readonly logger = new Logger(EventsController.name);
      	...
        
        
        this.logger.log('xxx') // also debug warm ...
      	...
      }
      ```

* Exception

  * ```ts
      @Get(':id')
      async findOne(@Param('id', ParseIntPipe) id: number) {
        // console.log(typeof id);
        const event = await this.repository.findOne(id);
      
        if (!event) {
          throw new NotFoundException(); // it will return 404 error code
        }
      
        return event;
      }
    ```



# Database Concept

* One to many relations

  * ```ts
    // event.entity.ts
    import { Column, Entity, OneToMany, PrimaryGeneratedColumn } from 'typeorm';
    import { Attendee } from './attendee.entity';
    
    @Entity()
    export class Event {
      @PrimaryGeneratedColumn()
      id: number;
    
      @Column()
      name: string;
    
      @Column()
      description: string;
    
      @Column()
      when: Date;
    
      @Column()
      address: string;
    
      @OneToMany(() => Attendee, (attendee) => attendee.event)
      attendees: Attendee[];
    }
    ```

  * ```ts
    // attendee.entity.ts
    import {
      Column,
      Entity,
      JoinColumn,
      ManyToOne,
      PrimaryGeneratedColumn,
    } from 'typeorm';
    import { Event } from './event.entity';
    
    @Entity()
    export class Attendee {
      @PrimaryGeneratedColumn()
      id: number;
    
      @Column()
      name: string;
    
      @ManyToOne(
        // mandatory with @OneToMany
        () => Event, // type of relation
        (event) => event.attendees, // field in Event entity that points to this entity
        { nullable: false }, // optionality of relation
      )
      @JoinColumn({
        // configure foreign key column
        name: 'event_id', // name of column in this entity
        referencedColumnName: 'id', // name of column in Event entity, default is primary key
      })
      event: Event;
    }
    
    ```

  * Remember to add `Attendee` into `orm.config.ts`

* read related entities
  * Relation has two different loading ways:
    * eager load - always load related entities
      * implemeted by using `join` in SQL to get all related tables
      * it can be set always eager on `@OneToMany(..,.., {eager: true})`
        * it can be temporarily configged by `xxx.findOne(..., {loadEagerRelations: <true|false>})`
          * this will overwrite the entity configuration
        * if has multi relations: can config `xxx.findOne(..., {relations: ['<relationColName>']})`to load specific relationss
    * lazy load - load related entities on demand

* Associating Related Entities
  * use cascade to pass certain operations to related entities
    * `@OneToMany(() => Attendee, (attendee) => attendee.event, {cascade: ["insert", "update"]})`
      * it pass insert and update operations. Therefore, when save or update `event` record, it will also insert or update `attendee`records
      * it can be directly set to true for passing all operations to related entities 
  * Take care for this part

* Many-to-Many relations

  * ```ts
    // subject.entity.ts
    
    import { Column, Entity, JoinTable, ManyToMany, PrimaryGeneratedColumn } from "typeorm";
    import { Teacher } from './teacher.entity';
    
    @Entity()
    export class Subject {
      @PrimaryGeneratedColumn()
      id: number;
    
      @Column()
      name: string;
    
      @ManyToMany(
        () => Teacher, (teacher) => teacher.subjects, { cascade: true }
      )
      @JoinTable() // only need to decorate on one of two related entities
      teachers: Teacher[];
    }
    ```

  * ```ts
    // teacher.entity.ts
    import { Column, Entity, ManyToMany, PrimaryGeneratedColumn } from "typeorm";
    import { Subject } from './subject.entity';
    
    @Entity()
    export class Teacher {
      @PrimaryGeneratedColumn()
      id: number;
    
      @Column()
      name: string;
    
      @ManyToMany(() => Subject, (subject) => subject.teachers)
      subjects: Subject[];
    }
    ```

  * `@ManyToMany ` can decorate on one or both - 

    * related entities can be retrieved from decorated side

* Query Builder Introduction

  * make sql more reusable!

  * ```ts
    // events.service.ts 
    // also use service to handle task logic as provider
    import { InjectRepository } from '@nestjs/typeorm';
    import { Repository } from 'typeorm';
    import { Injectable, Logger } from '@nestjs/common';
    
    @Injectable()
    export class EventsService {
      private readonly logger = new Logger(EventsService.name);
      constructor(
        @InjectRepository(Event)
        private readonly eventsRepository: Repository<Event>,
      ) {}
    
      private getEventsBaseQuery() { // return a basic query 
        return this.eventsRepository
          .createQueryBuilder('e')
          .orderBy('e.id', 'DESC');
      }
    
      public async getEvent(id: number): Promise<Event | undefined> {
        const query = this.getEventsBaseQuery().andWhere('e.id = :id', {
          id,
        }); // extend the basic query based on specific requirement 
    
        this.logger.debug(query.getSql());
    
        return await query.getOne();
      }
    }
    
    ```

* joins and aggregation with query builder

  * count records - `xxx.getCount()`

  * add calculated property in event - `attendeeCount?: number;`

    * it does not have `@Column` decorator

    * ```ts
      // event.service.ts
      
      	...
        this.getEventsBaseQuery()
            .loadRelationCountAndMap( // used to count the number of related entities
              'e.attendeeCount', // created a new property matched the new optional property in event
              'e.attendees', // relation Name
            )
        ...
      ```

    * 

  * enum + more complicated calculated property

    * ```ts
      // attendee.entity.ts
      	...  
      
      	@Column('enum', {
          enum: AttendeeAnswerEnum,
          default: AttendeeAnswerEnum.Accepted,
        })
        answer: AttendeeAnswerEnum;
      
      	...
      ```

    * ```ts
      // event.entity.ts
      	...
        // more calculated properties
        attendeeRejected?: number;
        attendeeAccepted?: number;
        attendeeMaybe?: number;
      	...
      ```

    * ```ts
      // event.service.ts
      			...
            .loadRelationCountAndMap(
              'e.attendeeMaybe', 
              'e.attendees',
              'attendee', // alia name used in below function, which stands for Attendee Class in this example
              (qb) =>
                qb.where('attendee.answer = :answer', {
                  answer: AttendeeAnswerEnum.Maybe,
                }),
            );
      			...
      ```

* Filtering data using Query Builder

  * Use `@Query()` decorator - tips [difference between `@Param` and `@Query`](https://dev.to/nandhakumar/nest-js-tutorial-3-query-route-params-3gi4)
  * use `xxx.andWhere(..)`to add additional filtering

* Pagination - not really practical

* using QB for updating, deleting, modifying relations

  * deleting

    * ```ts
      await this.eventsRepository
            .createQueryBuilder('e')
            .delete()
            .where('e.id = :id', { id })
            .execute();
      ```

  * updating

    * ```ts
      await this.subjectRepository
            .createQueryBuilder('s')
            .update()
            .set({ name: 'Confidential' })
            .execute();
      ```

  * adding

    * ```ts
      await this.subjectRepository
            .createQueryBuilder()
            .relation(Subject, 'teachers')
            .of(subject)
            .add([teacher1, teacher2]);
      ```

* one to one relation

  * ```ts
    // user.entity.ts
    import {
      Column,
      Entity,
      JoinColumn,
      OneToOne,
      PrimaryGeneratedColumn,
    } from 'typeorm';
    import { Profile } from './profile.entity';
    
    @Entity()
    export class User {
      @PrimaryGeneratedColumn()
      id: number;
    
      @Column()
      username: string;
      @Column()
      password: string;
      @Column()
      firstName: string;
      @Column()
      lastName: string;
      @Column()
      email: string;
    
      @OneToOne(() => Profile, (profile) => profile.user)
      @JoinColumn() // add profileId column in User table, instead of a new table
      profile: Profile;
    }
    ```

  * ```ts
    // profile.entity.ts
    import { Column, Entity, PrimaryGeneratedColumn } from 'typeorm';
    
    @Entity()
    export class Profile {
      @PrimaryGeneratedColumn()
      id: number;
      @Column()
      age: number;
      
      @OneToOne(() => User, (user) => user.profile)
      user: User;
    }
    
    ```
  
* most of relations can be used individually, but `@OneToMany()`

# Authentication, JWT, Authorisation

* <img src="/Users/ian.wang/Documents/notes/NoteRepository/开发/NestJS.assets/image-20230926095653706.png" alt="image-20230926095653706" style="zoom:50%;" />

* Installation

  * `npm install --save @nestjs/passport passport passport-local`
  * `npm install --save-dev @types/passport-local`

* Passport.js - authentication component

  * local strategy - using username + password to authenticate

    * ```ts
      // local.strategy.ts
      import { Injectable, Logger, UnauthorizedException } from '@nestjs/common';
      import { PassportStrategy } from '@nestjs/passport';
      import { Strategy } from 'passport-local';
      import { Repository } from 'typeorm';
      import { User } from './user.entity';
      import { InjectRepository } from '@nestjs/typeorm';
      
      
      @Injectable()
      export class LocalStrategy extends PassportStrategy(Strategy, 'local') { // default name is 'local'
        private readonly logger = new Logger(LocalStrategy.name);
      
        constructor(
          @InjectRepository(User) 
          private readonly userRepository: Repository<User>,
        ) {
          super();
        }
        public async validate(username: string, password: string): Promise<any> { // will be invoked by AuthGuard('local')
          const user = await this.userRepository.findOne({
            where: { username },
          });
      
          if (!user) {
            this.logger.debug(`User ${username} not found!`);
            throw new UnauthorizedException();
          }
      
          if (password !== user.password) {
            this.logger.debug(`Password ${password} is invalid!`);
            throw new UnauthorizedException();
          }
      
          return user;
        }
      }
      ```

    * ```ts
      // auth.controller.ts
      import { Controller, Post, Request, UseGuards } from '@nestjs/common';
      import { AuthGuard } from '@nestjs/passport';
      
      @Controller('auth')
      export class AuthController {
        @Post('login')
        @UseGuards(AuthGuard('local')) // it will invoke the validate() method in local.strategy.ts 
        async login(@Request() request) {
          // request includes username and password required by validate method in local.strategy.ts
          // username and password are stored in request body
          return {
            userId: request.user.id,
            token: 'the token will go here',
          };
        }
      }
      ```

    * difference between `@Body` and `@Request`

      * `@Request` has access to all information inside request
        * including header, query parameters, method, URL and more

      * `@Body` can convert json-based body in request into specific DTO

  * JWT strategy - using token to authenticate

    * ![image-20230926131849321](/Users/ian.wang/Documents/notes/NoteRepository/开发/NestJS.assets/image-20230926131849321.png)

    * install JWT - 

      * `npm install --save @nestjs/jwt passport-jwt`
      * `npm install --save-dev @types/passport-jwt`

    * register jwt

      * ```ts
        // auth.module.ts
        @Module({
         	imports: [
            TypeOrmModule.forFeature([User]),
            JwtModule.registerAsync({ // use async function can avoid running jwt before .env file is loaded
              useFactory: () => ({
                secret: 'secret123', // should be declared in more secure way, such as .env file - process.env.<VAR>
                signOptions: { expiresIn: '60m' },
              }),
            }),
          ],
          providers: [LocalStrategy, AuthService],
          controllers: [AuthController],
        })
        export class AuthModule {}
        ```

    * genrate token

      * ```ts
        // auth.service.ts
        import { JwtService } from '@nestjs/jwt';
        import { Injectable } from '@nestjs/common';
        import { User } from './user.entity';
        
        @Injectable()
        export class AuthService {
          constructor(private readonly jwtService: JwtService) {}
        
          public getTokenForUser(user: User): string { // manually invoke this 
            return this.jwtService.sign({
              username: user.username,
              sub: user.id,
            });
          }
        }
        ```

      * ```ts
        // auth.controller.ts
        @Controller('auth')
        export class AuthController {
          constructor(private readonly authService: AuthService) {} // add new dependency
          @Post('login')
          @UseGuards(AuthGuard('local'))
          async login(@Request() request) {
            return {
              userId: request.user.id,
              token: this.authService.getTokenForUser(request.user), // generate JWT token
            };
          }
        }
        ```

    * use jwt to authenticate

      * ```ts
        import { PassportStrategy } from '@nestjs/passport';
        import { ExtractJwt, Strategy } from 'passport-jwt';
        import { Injectable } from '@nestjs/common';
        import { Repository } from 'typeorm';
        import { InjectRepository } from '@nestjs/typeorm';
        import { User } from './user.entity';
        
        @Injectable()
        export class JwtStrategy extends PassportStrategy(Strategy) { // so this one default name is jwt, because this PassportStrategy is imported from different lib
          
        
          constructor(
            @InjectRepository(User) private readonly userRepository: Repository<User>,
          ) {
            super({
              jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(), // get token from xxx
              ignoreExpiration: false, // reject expired token
              secretOrKey: process.env.AUTH_SECRET, // signing key
            });
          }
        
          async validate(payload: any) {
            return await this.userRepository.findOne(payload.sub);
          }
        }
        
        ```

      * ```ts
        import { Controller, Get, Post, Request, UseGuards } from '@nestjs/common';
        import { AuthGuard } from '@nestjs/passport';
        import { AuthService } from './auth.service';
        
        @Controller('auth')
        export class AuthController {
          constructor(private readonly authService: AuthService) {}
          
          async login(@Request() request) {
           ...
          }
        
          @Get('profile')
          @UseGuards(AuthGuard('jwt')) // it will invoke the validate() method in jwt.strategy.ts based on type
          async getProfile(@Request() request) {
            return request.user;
          }
        }
        
        ```


# Testing

* overview
  * ![image-20231003104600353](/Users/ian.wang/Documents/notes/NoteRepository/开发/NestJS.assets/image-20231003104600353.png)
* install testing lib
  * Install - `npm install --save-dev @nestjs/testing`
* check coverage
  * `npm run test:cov`

## Unit Testing

* demo

  * ```ts
    import { EventsService } from './events.service';
    import { EventsController } from './events.controller';
    import { Repository } from 'typeorm';
    import { ListEvents } from './list.events';
    import { User } from '../auth/user.entity';
    import { NotFoundException } from '@nestjs/common';
    
    describe('EventsController', () => {
      let eventsService: EventsService;
      let eventsController: EventsController;
      let eventsRepository: Repository<Event>;
      
      // overall setup function
      beforeAll(() => {
        console.log('this logged once');
      });
      
      // individual setup function
      beforeEach(() => {
        console.log('this logged before each test');
        eventsService = new EventsService(eventsRepository);
        eventsController = new EventsController(eventsService);
      });
    
      // it === test
      it('should return a list of event', async () => {
        const result = {
          first: 1,
          last: 1,
          limit: 10,
          data: [],
        };
    
        // simple way to mock
        // eventsService.getEventsWithAttendeeCountFilteredPaginated = jest
        //   .fn()
        //   .mockImplementation((): any => result);
        
        // alternative way to mock with more customized config
        const spy = jest
          .spyOn(eventsService, 'getEventsWithAttendeeCountFilteredPaginated')
          .mockImplementation((): any => result);
        expect(await eventsController.findAll(new ListEvents())).toEqual(result);
        expect(spy).toBeCalledTimes(1);
      });
    
      it("should not delete an event, when it's not found", async () => {
        const deleteSpy = jest.spyOn(eventsService, 'deleteEvent');
        const findSpy = jest
          .spyOn(eventsService, 'getEvent')
          .mockImplementation(() => undefined);
    
        try {
          await eventsController.remove(1);
        } catch (e) {
          expect(e).toBeInstanceOf(NotFoundException);
        }
        expect(deleteSpy).not.toHaveBeenCalled();
        expect(findSpy).toHaveBeenCalledTimes(1);
      });
      
      // also some teardown functions
      // afterall
      // afterEach
    });
    
    ```

* Mock module

  * ```ts
    import { getRepository, Repository } from 'typeorm';
    import { EventsService } from './events.service';
    import { Test } from '@nestjs/testing';
    import { getRepositoryToken } from '@nestjs/typeorm';
    
    describe('EventsService', () => {
      let service: EventsService;
      let repository: Repository<Event>;
    
      beforeEach(async () => {
        const module = await Test.createTestingModule({
          providers: [
            EventsService,
            {
              provide: getRepositoryToken(Event), // get Repository<Event>
              useValue: {
                save: jest.fn(),
                createQueryBuilder: jest.fn(),
                delete: jest.fn(),
                where: jest.fn(),
                execute: jest.fn(),
              },
            },
          ],
        }).compile();
    
        service = module.get<EventsService>(EventsService);
        repository = module.get<Repository<Event>>(getRepositoryToken(Event));
      });
      
      ...
    });
    ```

* Complex test structure
  * <img src="/Users/ian.wang/Documents/notes/NoteRepository/开发/NestJS.assets/image-20231003120530741.png" alt="image-20231003120530741" style="zoom:50%;" />
* some tips
  * get mock return - `jest.fn().mockReturnValue()`
    * unwrap promise - `xxx.mockResolvedReturnValue()`
  * mock whole component - `jest.mock('xxx/xxx/<fileName>/<exportComponentName>')`
  * make sure mock function always return some in chain calling.
  * use `resolves` to unwrap promise\<xxx> - `...resloves.toEqual(...)`

## E2E Testing

* <img src="/Users/ian.wang/Documents/notes/NoteRepository/开发/NestJS.assets/image-20231003122632208.png" alt="image-20231003122632208" style="zoom:50%;" />
* 
