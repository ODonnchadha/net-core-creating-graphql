## Creating GraphQL APIs With ASP.Net Core For Beginners

- INTRODUCTION:
    - WHAT is it? Open-source, Facebook-developed, language for APIs, supporting various coding languages with types and fields.
    - FAST and stable single endpoint where the client can specify the structure and retrieve multiple, real-time data is a single request.
    - USED by yelp, PayPal, GitHub, and airbnb.
    - GRAPHQL versus REST:
        - REPRESENTATIONAL State Transfer with architectural style for Web services using HTTP methods and HTTP codes.

- QUERYING DATA FROM GITHUB:
    - USING GitHub GraphQL [GitHub GraphQL](https://docs.github.com/en/graphql/overview/explorer).
    - FRAGMENTS: 
        ```javascript
            {
                user(login:"ODonnchadha"){
                    ...userFields
                }
            }
            fragment userFields on User {
                user_id:id
                name
                url
                bio
            }
        ```
    - VARIABLES: Note: ! denota a required variable. Dynamic. Seperate dictionary. Default value.
        ```javascript
            query ($loginName: String!) {
                user(login:$loginName) {
                ... on Actor {
                    login
                    url
                }
                ... on ProfileOwner {
                    id
                    name
                    }
                bio
                }
            }

            {
                "loginName": "ODonnchadha"
            }
        ```
    - DIRECTIVES: Change query structure. Attach to field or fragments. @Include & @Skip. e.g.: @DirectiveName(if: Boolean)
        ```javascript
            query($loadStatus: Boolean!) {
                viewer {
                    id
                    name
                    login
                    url
                    location
                    bio @skip(if: $loadStatus)
                    status @include(if: $loadStatus){
                        emoji
                        message
                    }
                }
            }
            {
                "loadStatus": true
            }
        ```
    - PAGING: Change query structure. Attach to field or fragments. @Include & @Skip. e.g.: @DirectiveName(if: Boolean)
        ```javascript
            query ($loginName: String = "ODonnchadha") {
                user(login:$loginName) {
                    id
                    name
                    login
                    url
                    location
                    bio
                    repositories(first: 5, after:"Y3Vyc29yOnYyOpHODg5mvA==") {
                        totalCount
                        pageInfo {
                            hasPreviousPage
                            hasNextPage
                            startCursor
                            endCursor
                        }
                        edges {
                            cursor
                            node {
                                name
                                url
                            }
                        }
                    }
                }
            }
        ```

- SCHEMA & TYPES:
    - SCALAR and object type:
        - GRAPHQL schema: (1) Supported queries, mutations, fields, types. (2) Used to determine if a query is valid. (3) GraghQL schema language.
        - TYPES: (1) Basic components. (2) Send/receive by server. 
            - (a) Scalar: Primitive value. Integer, Float, String, Boolean, and ID. Add/Remove. e.g.: scalar Date.
            - (b) Object: Specify fields. Describe a tree of information. Fields can be scalar or object.
            ```javascript
                type User { 
                    id: ID
                    userName: String
                    mailId: String
                    contactDetails: ContactDetails
                }
                type ContactDetails {
                    street: String
                }
            ```
            - (c) Interface: Abstract. Use when returning multiple types of an object. Multiple interfaces.
            ```javascript
                interface BaseUser { 
                    userName: String
                    password: String
                }
                interface BaseUserAdmin { 
                    canManageUsers: Boolean
                }
                type User implements BaseUser & BaseUserAdmin {
                    mailId: String
                }
            ```
            - (d) Union: Multiple object return. May have different fields. Could be one of a list of GraphQL object types.
                ```javascript
                    type WebResult { 
                        webpageUrl: Url
                        description: String
                    }
                    type PhotoResult { 
                        imageUrl: Url
                        height: Int
                        width: Int
                    }
                    union SearchResult = WebResult | PhotoResult
                    type SearchQuery {
                        searchResult: SearchResult
                    }

                    query {
                        searchResult {
                            ... on WebResult {
                                webpageUrl
                            }
                            ... on PhotoResult {
                                imageUrl
                            }
                        }
                    }
                ```
            - (e) Enum: Define set of possible values. Scalar type. Serialize as string. Useful in arguments.
            - (f) InputObjectType: Complex data within argument. Mutations. Input keyword. Fields can be scalar, enum, or InputObject type.
                ```javascript
                    input AddressDetails {
                        street: String
                        city: String
                    }
                ```
        - LIST and not null:
            - LIST: Multiple values. Same type. Wrapped in []. Use both scalar & object type:
                ```javascript
                    type TravelDetails {
                        countriesVisted: [CountriesVisited]
                    }
                ```

- ADVANCED GRAPHQL CONCEPTS: