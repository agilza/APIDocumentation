Step-by-step guide

Why to use? 
Need data from multiple resources at the same time.
Client can specify what data is desired.

Navigate child resources in single request and allow for multiple queries in single request.

GraphQL Schemas
Has to be located in classpath in the file with extension "*.graphqls"
Schema describing API with fields and nesting of Types.

type OFI {
    fiveDigitFundCode: String!
    fundCode: String!
    cusip: String!
    leiFundNumber: String!
}
 
type ProductXrefEntity {
 
    invescoTACode: String
    legal_name: String!
    abbreviatedDisplayName: String
    cusipNumber: String!
    oldCusip: String!
    leiFundNumber: String!
    oldLeiFundNumber: String
    fiveDigitFundCode: String
}
 
type Query {
    products: [ProductXrefEntity]!
    getProductByOfiId(ofiCode: String): [ProductXrefEntity]!
}
Fields correspond to the @Data Bean

  The “!” at the end of some names indicates that this is a non-nullable type. Any type that does not have this can be null in the response from the server.



Dependencies

<dependency>
    <groupId>com.graphql-java-kickstart</groupId>
    <artifactId>graphql-spring-boot-starter</artifactId>
    <version>5.10.0</version>
</dependency>
 
<!-- to embed Altair tool -->
<dependency>
    <groupId>com.graphql-java-kickstart</groupId>
    <artifactId>altair-spring-boot-starter</artifactId>
    <version>5.10.0</version>
    <scope>runtime</scope>
</dependency>
 
<!-- to embed GraphiQL tool -->
<dependency>
    <groupId>com.graphql-java-kickstart</groupId>
    <artifactId>graphiql-spring-boot-starter</artifactId>
    <version>5.10.0</version>
    <scope>runtime</scope>
</dependency>
 
<!-- to embed Voyager tool -->
<dependency>
    <groupId>com.graphql-java-kickstart</groupId>
    <artifactId>voyager-spring-boot-starter</artifactId>
    <version>5.10.0</version>
    <scope>runtime</scope>
</dependency>
 
<!-- testing facilities -->
<dependency>
    <groupId>com.graphql-java-kickstart</groupId>
    <artifactId>graphql-spring-boot-starter-test</artifactId>
    <version>5.10.0</version>
    <scope>test</scope>
</dependency>
SpringBoot configuration

graphql:
  servlet:
    mapping: /products
    enabled: true
Root Query Resolver
Special bean to handle various fields in root query, must implement GraphQLQueryResolver
names of the method must be 
<field>
is<field> – only if the field is of type Boolean
get<field>
must return the correct return type for the type in the GraphQL schema.


public class Query implements GraphQLQueryResolver {
    private ProductXrefRepository productXrefRepository;
 
    public Query(ProductXrefRepository productXrefRepository) {
        this.productXrefRepository = productXrefRepository;
    }
 
    Iterable<ProductXrefEntity> products() {
        return productXrefRepository.getProductXrefEntities();
    }
 
 
    List<ProductXrefEntity> getProductByOfiId(String ofiCode) throws Exception{ .... }
}
 
 
@Bean
public Query query(ProductXrefRepository productXrefRepository) {
 
    return new Query(productXrefRepository);
}
Field Resolver


 
Requests


{
  products {
    fiveDigitFundCode
    invescoTACode
    legal_name
  }
}
 
 
{
  getProductByOfiId(ofiCode: "257") {
    fiveDigitFundCode
    invescoTACode
    legal_name
  }
}


See example of adding GraphQl capability to existing service:  https://bitbucket.internal.ofiglobal.net/projects/EA/repos/product-xref/browse?at=graphql-poc

 



http://localhost:8080/graphiql - Graphical UI

http://localhost:8080/graphql



GraphQL:

https://www.graphql-java.com/tutorials/getting-started-with-spring-boot/

https://graphql.org/learn/schema/

https://www.graphql-java.com/documentation/master/schema/

https://graphql.org/learn/queries/

https://graphql.org/learn/schema/#the-query-and-mutation-types

https://github.com/graphql-java-kickstart/graphql-java-tools

https://github.com/graphql-java-kickstart/graphql-spring-boot#usage

https://github.com/graphql-java-kickstart/graphql-spring-boot/tree/master/example-spring-common



SPQR: https://github.com/leangen/graphql-spqr-spring-boot-starter

Still in development - ALPHA



StackOverflow:

https://stackoverflow.com/questions/51325556/graphql-and-spring-boot-2-0-3

https://stackoverflow.com/questions/tagged/graphql-java?sort=newest&page=2&pagesize=15



https://dev.to/sambenskin/howto-integrate-a-mysql-database-into-your-java-spring-boot-graphql-service-26c



Conclusion
GraphQL and supporting SpringBoot libraries are very simple to use.

However, GraphQL breaks REST principle and does not use GET/POST concept, every request is POST. 

Simple browser cannot be used to get information from service. GraphiQl or new version of Postman can be exposed to make a sample request.

GraphQL requires to specify fields you want to see in response, so if you are dealing with large payload it might become impractical.

Client should be able to create and receive GraphQl payloads.

I do not think GraphQl should be recommended for general use.

