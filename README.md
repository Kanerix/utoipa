# utoipa - Autogenerate OpenAPI documentation

Want to document your utopic api? Want it to feel as fuzzy as cold ipa? Then you are probably in need of utoipa.  

Utoipa crate provides autogenerated OpenAPI documentation for Rust REST APIs. It treats code first appoach as a
first class citizen and simplifies API documentation by providing simple macros for generating the 
documentation from your code. 

It also contains Rust types of OpenAPI spec allowing you to write the OpenAPI spec only using Rust if
autogeneration is not your flavor or does not fit your purpose. 

In future there are plans to support more sophisticated code generation from generated OpenAPI spec and as well 
to support contract first approach.

## Current project status

**This project is currently in active development and not ready for PRODUCTION use!** 

The reference implementation is almost there which enables people to play around with the library. It is yet to be released to crates later on when the api and the functionalities are mature enough for wider scale adoption. 

See https://github.com/juhaku/utoipa/projects for more details about the progress of the project implementation.

## 30 sec Lesson

Create type for API. Crusial part is the `Component` derive which will create OpenAPI component for the struct or enum. 
```rust
#[derive(Deserialize, Serialize, Component)]
struct Pet {
    id: u64,
    name: String,
    age: Option<i32>,
}
```

Create your Get pet API.
```rust
mod pet_api {
    /// Get pet by id
    ///
    /// Get pet from database by pet database id  
    #[utoipa::path(
        get,
        path = "/pets/{id}"
        responses = [
            (status = 200, description = "Pet found succesfully", body = Pet),
            (status = 404, description = "Pet was not found")
        ],
        params = [
            ("id" = u64, path, description = "Pet database id to get Per for"),
        ]
    )]
    async fn get_pet_by_id(pet_id: u64) -> Pet {
        Pet {
            id: pet_id,
            age: None,
            name: "lightning".to_string(),
        }
    }
}
```

Tie the API and Component together with the `OpenApi` derive which will create OpenAPI api doc for you.
```rust
#[derive(OpenApi, Default)]
#[openapi(handlers = [pet_api::get_pet_by_id], components = [Pet])]
struct ApiDoc;

println!("{}", ApiDoc::openapi().to_pretty_json().unwrap());
```

This would produce api doc something similar to:
```json
{
  "openapi": "3.0.3",
  "info": {
    "title": "application name from Cargo.toml",
    "description": "description from Cargo.toml",
    "contact": {
      "name": "author name from Cargo.toml",
      "email":"author email from Cargo.toml"
    },
    "licence": {
      "name": "license from Cargo.toml"
    },
    "version": "version from Cargo.toml"
  },
  "paths": {
    "/pets/{id}": {
      "get": {
        "tags": [
          "pet_api"
        ],
        "summary": "Get pet by id",
        "description": "Get pet by id\n\nGet pet from database by pet database id\n",
        "operationId": "get_pet_by_id",
        "parameters": [
          {
            "name": "id",
            "in": "path",
            "description": "Pet database id to get Per for",
            "required": true,
            "deprecated": false,
            "schema": {
              "type": "integer",
              "format": "int64"
            }
          }
        ],
        "responses": {
          "200": {
            "description": "Pet found succesfully",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/Pet"
                }
              }
            }
          },
          "404": {
            "description": "Pet was not found"
          }
        },
        "deprecated": false
      }
    }
  },
  "components": {
    "schemas": {
      "Pet": {
        "type": "object",
        "required": [
          "id",
          "name"
        ],
        "properties": {
          "id": {
            "type": "integer",
            "format": "int64"
          },
          "name": {
            "type": "string"
          },
          "age": {
            "type": "integer",
            "format": "int32"
          }
        }
      }
    }
  }
}
```

## Goals

Eeasy of use, and easy of development along with avoinding excessive cloning and keeping perfomance in 
mind while developing the library. It should follow the general rust quidelines and latest conventions 
of the Rust.  

Goal|Status
-|-
Necessary subset implementation of OpenAPI spec in Rust| :heavy_minus_sign: Some definitions missing
Macros for autogenerating documentation | :heavy_check_mark: Done, but some things missing 
Embed Swagger UI for OpenAPI documentation | :heavy_check_mark: Done
Embed ReDocly for OpenAPI documentation | :x: Not done 
Integration with Actix web framework | :heavy_minus_sign: In development
Integration with Rocker framework | :x: Not done
Write exessive documentation & Book & Examples | :x: Not done
At least 90 % tested code | :heavy_minus_sign: Still ongoing along with the other development

Integration with other frameworks are not planned as of this stage but when the intial set of features are 
complete the integrations with other frameworks can be prioritized.

# License

Licensed under either of [Apache 2.0](LICENSE-APACHE) or [MIT](LICENSE-MIT) license at your option.

Unless you explicitly state otherwise, any contribution intentionally submitted for inclusion in this crate 
by you, shall be dual licensed, without any additional terms or conditions. 
