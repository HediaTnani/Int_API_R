# Intermediate API in R

## 1. Basic API in R

Lat time we saw how to make a `GET` request to retrieve data from an
API. We'll remember again the different steps. We'll also add query
parameters and authentication.

Let's remember what we did last time using the Ensembl Rest API.

First we prepare the URL.

::: cell
``` {.r .cell-code}
library(httr) # accessing APIs
# Preparing the URL 
baseURL <- "https://rest.ensembl.org"
ext <- "/regulatory/species/homo_sapiens/microarray?"
URL <- modify_url(baseURL, path = ext)
```
:::

As we saw last time a **Uniform Resource Identifier (URI)** **resource**
specifies which resource you want to access. A URI is a generalization
of a URL (Uniform Resource Locator)---what you commonly think of as
\"web addresses\".

Then we query the database.

::: cell
``` {.r .cell-code}
response = GET(URL)
response
```

::: {.cell-output .cell-output-stdout}
    Response [https://rest.ensembl.org/regulatory/species/homo_sapiens/microarray]
      Date: 2023-03-16 14:39
      Status: 200
      Content-Type: application/json
      Size: 3.46 kB
:::
:::

Then we convert the JSON format to a dataframe by using the `fromJSON`
function.

::: cell
``` {.r .cell-code}
library(jsonlite)
fromJSON(content(response,as = "text", encoding = "UTF-8" ))
```

::: {.cell-output .cell-output-stdout}
                         description   vendor      format  type
    1                           <NA>  PHALANX  EXPRESSION OLIGO
    2                           <NA> CODELINK  EXPRESSION OLIGO
    3                           <NA> ILLUMINA  EXPRESSION OLIGO
    4                           <NA>  AGILENT  EXPRESSION OLIGO
    5                           <NA> ILLUMINA METHYLATION OLIGO
    6                           <NA> ILLUMINA  EXPRESSION OLIGO
    7                           <NA>  AGILENT  EXPRESSION OLIGO
    8                           <NA>  AGILENT         CGH OLIGO
    9                           <NA>  AGILENT  EXPRESSION OLIGO
    10                          <NA>     AFFY  EXPRESSION OLIGO
    11                          <NA>  AGILENT  EXPRESSION OLIGO
    12                          <NA>     AFFY  EXPRESSION OLIGO
    13                          <NA>  AGILENT  EXPRESSION OLIGO
    14                          <NA>  AGILENT  EXPRESSION OLIGO
    15                          <NA>  AGILENT  EXPRESSION OLIGO
    16                          <NA>     AFFY  EXPRESSION OLIGO
    17                          <NA>  AGILENT  EXPRESSION OLIGO
    18                          <NA> ILLUMINA METHYLATION OLIGO
    19                          <NA>     AFFY  EXPRESSION OLIGO
    20 Human Transcriptome Array 2.0     AFFY  EXPRESSION OLIGO
    21                          <NA>     AFFY  EXPRESSION OLIGO
    22                          <NA>     AFFY  EXPRESSION OLIGO
    23                          <NA>     AFFY  EXPRESSION OLIGO
    24                          <NA>     AFFY  EXPRESSION OLIGO
    25                          <NA>     AFFY  EXPRESSION OLIGO
    26                          <NA>     AFFY  EXPRESSION OLIGO
    27                          <NA>     AFFY  EXPRESSION OLIGO
    28                          <NA>     AFFY  EXPRESSION OLIGO
    29                          <NA>     AFFY  EXPRESSION OLIGO
    30                          <NA>     AFFY  EXPRESSION OLIGO
    31                          <NA>     AFFY  EXPRESSION OLIGO
    32                          <NA>     AFFY  EXPRESSION OLIGO
    33                          <NA>     AFFY  EXPRESSION OLIGO
    34                          <NA>     AFFY  EXPRESSION OLIGO
    35                          <NA>     AFFY  EXPRESSION OLIGO
                          array
    1                  OneArray
    2                  CODELINK
    3              HumanWG_6_V3
    4                   GPL6848
    5       HumanMethylation450
    6             HumanRef-8_V3
    7               WholeGenome
    8                   CGH_44b
    9     SurePrint_G3_GE_8x60k
    10                  HC-G110
    11 SurePrint_G3_GE_8x60k_v2
    12                 HG-Focus
    13     WholeGenome_4x44k_v1
    14     WholeGenome_4x44k_v2
    15                 GPL26966
    16               HG-U133A_2
    17                 GPL19072
    18       HumanMethylation27
    19                 HG-U133B
    20                  HTA-2_0
    21           HG-U133_Plus_2
    22                HG_U95Av2
    23                  HG-U95B
    24                  HG-U95C
    25                  HG-U95D
    26                  HG-U95E
    27                  HG_U95A
    28                 HuGeneFL
    29                 U133_X3P
    30                PrimeView
    31       HT_HG-U133_Plus_PM
    32           HuEx-1_0-st-v2
    33         HuGene-2_0-st-v1
    34         HuGene-1_0-st-v1
    35         HuGene-2_1-st-v1
:::
:::

Then we saw how to make a function.

::: cell
``` {.r .cell-code}
library(httr)
library(jsonlite)

get_microarrays <- function(baseURL, ext, content_type){

    r <- GET(paste(baseURL, ext, sep = ""), accept(content_type))

    stop_for_status(r)

    if (content_type == 'application/json'){
        return (fromJSON(content(r, "text", encoding = "UTF-8")))
    } else {
        return (content(r, "text", encoding = "UTF-8"))
    }
}

baseURL <- "https://rest.ensembl.org"
ext <- "/regulatory/species/homo_sapiens/microarray?"
con <- "application/json"


get_microarrays(baseURL, ext, con)
```

::: {.cell-output .cell-output-stdout}
                         description      format  type   vendor
    1                           <NA>  EXPRESSION OLIGO  PHALANX
    2                           <NA>  EXPRESSION OLIGO CODELINK
    3                           <NA>  EXPRESSION OLIGO ILLUMINA
    4                           <NA>  EXPRESSION OLIGO  AGILENT
    5                           <NA> METHYLATION OLIGO ILLUMINA
    6                           <NA>  EXPRESSION OLIGO ILLUMINA
    7                           <NA>  EXPRESSION OLIGO  AGILENT
    8                           <NA>         CGH OLIGO  AGILENT
    9                           <NA>  EXPRESSION OLIGO  AGILENT
    10                          <NA>  EXPRESSION OLIGO     AFFY
    11                          <NA>  EXPRESSION OLIGO  AGILENT
    12                          <NA>  EXPRESSION OLIGO     AFFY
    13                          <NA>  EXPRESSION OLIGO  AGILENT
    14                          <NA>  EXPRESSION OLIGO  AGILENT
    15                          <NA>  EXPRESSION OLIGO  AGILENT
    16                          <NA>  EXPRESSION OLIGO     AFFY
    17                          <NA>  EXPRESSION OLIGO  AGILENT
    18                          <NA> METHYLATION OLIGO ILLUMINA
    19                          <NA>  EXPRESSION OLIGO     AFFY
    20 Human Transcriptome Array 2.0  EXPRESSION OLIGO     AFFY
    21                          <NA>  EXPRESSION OLIGO     AFFY
    22                          <NA>  EXPRESSION OLIGO     AFFY
    23                          <NA>  EXPRESSION OLIGO     AFFY
    24                          <NA>  EXPRESSION OLIGO     AFFY
    25                          <NA>  EXPRESSION OLIGO     AFFY
    26                          <NA>  EXPRESSION OLIGO     AFFY
    27                          <NA>  EXPRESSION OLIGO     AFFY
    28                          <NA>  EXPRESSION OLIGO     AFFY
    29                          <NA>  EXPRESSION OLIGO     AFFY
    30                          <NA>  EXPRESSION OLIGO     AFFY
    31                          <NA>  EXPRESSION OLIGO     AFFY
    32                          <NA>  EXPRESSION OLIGO     AFFY
    33                          <NA>  EXPRESSION OLIGO     AFFY
    34                          <NA>  EXPRESSION OLIGO     AFFY
    35                          <NA>  EXPRESSION OLIGO     AFFY
                          array
    1                  OneArray
    2                  CODELINK
    3              HumanWG_6_V3
    4                   GPL6848
    5       HumanMethylation450
    6             HumanRef-8_V3
    7               WholeGenome
    8                   CGH_44b
    9     SurePrint_G3_GE_8x60k
    10                  HC-G110
    11 SurePrint_G3_GE_8x60k_v2
    12                 HG-Focus
    13     WholeGenome_4x44k_v1
    14     WholeGenome_4x44k_v2
    15                 GPL26966
    16               HG-U133A_2
    17                 GPL19072
    18       HumanMethylation27
    19                 HG-U133B
    20                  HTA-2_0
    21           HG-U133_Plus_2
    22                HG_U95Av2
    23                  HG-U95B
    24                  HG-U95C
    25                  HG-U95D
    26                  HG-U95E
    27                  HG_U95A
    28                 HuGeneFL
    29                 U133_X3P
    30                PrimeView
    31       HT_HG-U133_Plus_PM
    32           HuEx-1_0-st-v2
    33         HuGene-2_0-st-v1
    34         HuGene-1_0-st-v1
    35         HuGene-2_1-st-v1
:::
:::

## 2. Intermediate API in R

### **2.1. Adding Query Parameters**

What if we need to add query parameters ?

Query parameters are the extra **parameters** (arguments) about what
resource to access.

We can pass `query` parameters as a named list, where the names are the
`API` fields for each query parameter.

We can use the `query` parameter of the **`GET()` function** from the
`httr` package to add the query parameters to our request.

So let's edit the previous `get_microarrays()` function.

-   Add a **new parameter** named `queries` to the function and assign
    `list()` to it to make it an optional parameter: `queries = list()`.
    We assign a `list()` here so that multiple optional parameters can
    be passed to our function.

-   Edit the `GET()` function to set its `query` parameter to our
    function parameter `queries`.

Let's take again the same example using the Ensembl rest API.

::: cell
``` {.r .cell-code}
library(httr)
library(jsonlite)

get_microarrays <- function(baseURL, endpoint, queries){

    r <- GET(paste(baseURL, endpoint, sep = ""), queries)

    stop_for_status(r)

    return (content(r, "text", encoding = "UTF-8"))
    }

baseURL <- "https://rest.ensembl.org"
endpoint <- "/regulatory/species/homo_sapiens/microarray?"
queries = list(format="json")


get_microarrays(baseURL, endpoint, queries)
```

::: {.cell-output .cell-output-stdout}
    [1] "[{\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"vendor\":\"PHALANX\",\"array\":\"OneArray\",\"description\":null},{\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"array\":\"CODELINK\",\"vendor\":\"CODELINK\",\"description\":null},{\"array\":\"HumanWG_6_V3\",\"vendor\":\"ILLUMINA\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"description\":null},{\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"array\":\"GPL6848\",\"vendor\":\"AGILENT\",\"description\":null},{\"type\":\"OLIGO\",\"format\":\"METHYLATION\",\"vendor\":\"ILLUMINA\",\"array\":\"HumanMethylation450\",\"description\":null},{\"description\":null,\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"vendor\":\"ILLUMINA\",\"array\":\"HumanRef-8_V3\"},{\"array\":\"WholeGenome\",\"vendor\":\"AGILENT\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"description\":null},{\"vendor\":\"AGILENT\",\"array\":\"CGH_44b\",\"format\":\"CGH\",\"type\":\"OLIGO\",\"description\":null},{\"description\":null,\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"vendor\":\"AGILENT\",\"array\":\"SurePrint_G3_GE_8x60k\"},{\"array\":\"HC-G110\",\"vendor\":\"AFFY\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"description\":null},{\"array\":\"SurePrint_G3_GE_8x60k_v2\",\"vendor\":\"AGILENT\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"description\":null},{\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"vendor\":\"AFFY\",\"array\":\"HG-Focus\",\"description\":null},{\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"array\":\"WholeGenome_4x44k_v1\",\"vendor\":\"AGILENT\",\"description\":null},{\"description\":null,\"array\":\"WholeGenome_4x44k_v2\",\"vendor\":\"AGILENT\",\"format\":\"EXPRESSION\",\"type\":\"OLIGO\"},{\"description\":null,\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"array\":\"GPL26966\",\"vendor\":\"AGILENT\"},{\"vendor\":\"AFFY\",\"array\":\"HG-U133A_2\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"description\":null},{\"description\":null,\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"vendor\":\"AGILENT\",\"array\":\"GPL19072\"},{\"type\":\"OLIGO\",\"format\":\"METHYLATION\",\"vendor\":\"ILLUMINA\",\"array\":\"HumanMethylation27\",\"description\":null},{\"description\":null,\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"array\":\"HG-U133B\",\"vendor\":\"AFFY\"},{\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"vendor\":\"AFFY\",\"array\":\"HTA-2_0\",\"description\":\"Human Transcriptome Array 2.0\"},{\"description\":null,\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"vendor\":\"AFFY\",\"array\":\"HG-U133_Plus_2\"},{\"description\":null,\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"array\":\"HG_U95Av2\",\"vendor\":\"AFFY\"},{\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"array\":\"HG-U95B\",\"vendor\":\"AFFY\",\"description\":null},{\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"vendor\":\"AFFY\",\"array\":\"HG-U95C\",\"description\":null},{\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"array\":\"HG-U95D\",\"vendor\":\"AFFY\",\"description\":null},{\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"vendor\":\"AFFY\",\"array\":\"HG-U95E\",\"description\":null},{\"description\":null,\"vendor\":\"AFFY\",\"array\":\"HG_U95A\",\"format\":\"EXPRESSION\",\"type\":\"OLIGO\"},{\"description\":null,\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"vendor\":\"AFFY\",\"array\":\"HuGeneFL\"},{\"description\":null,\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"array\":\"U133_X3P\",\"vendor\":\"AFFY\"},{\"description\":null,\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"array\":\"PrimeView\",\"vendor\":\"AFFY\"},{\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"vendor\":\"AFFY\",\"array\":\"HT_HG-U133_Plus_PM\",\"description\":null},{\"description\":null,\"array\":\"HuEx-1_0-st-v2\",\"vendor\":\"AFFY\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\"},{\"description\":null,\"vendor\":\"AFFY\",\"array\":\"HuGene-2_0-st-v1\",\"format\":\"EXPRESSION\",\"type\":\"OLIGO\"},{\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"array\":\"HuGene-1_0-st-v1\",\"vendor\":\"AFFY\",\"description\":null},{\"description\":null,\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"array\":\"HuGene-2_1-st-v1\",\"vendor\":\"AFFY\"}]"
:::
:::

## 2.2 ChatGPT's **API Authentification**

To authenticate with the ChatGPT's API, we need to use an access token.
An access token is a credential we can **generate** on the OpenAI's
website. The token is a string that the API can read and associate with
our account.

Typically APIs use the following authentication types:

-   **Username-password authentication**: requires a username and
    password. We can use the `authenticate("username", "password")`
    function in the API requests to achieve this.

**API key authentication**: requires an API "key" or "token." We can use
the `api_key = "key"` parameter request to achieve this.

Access tokens can have scopes and specific permissions. Using
read-access-only tokens in potentially insecure or shared scripts
improves security.

APIs also use authentication for **rate limiting**. Developers typically
use APIs to build interesting applications or services. Ensuring an API
is available and responsive for all users will prevent us from making
too many requests too quickly. We call this **restriction rate
limiting**.

### 2.2.1 Create OpenAI API Keys

To use OpenAI through API, you must create a free account and generate
keys.

1\. Sign up
here [https://beta.openai.com/signup](https://beta.openai.com/signup){alt="https://beta.openai.com/signup"}.
You can use your Google or Microsoft account to sign up if you don't
want to create using an email/password combination. You may need a valid
mobile number to verify your account.

\
2. Now, visit your OpenAI key
page [https://beta.openai.com/account/api-keys](https://beta.openai.com/account/api-keys){alt="https://beta.openai.com/account/api-keys"} or
click the menu item "View API keys"

![](images/OpenAI_1.png){width="400"}

3\. Create a new key by clicking on **Personal** and then "Create new
secret key" button. See the image below for reference.

![](images/OpenAI_2.png)

Et voila !!!

OpenAI gives \$18 free credits which you can consume within the first 3
months.

### 2.2.2 Use OpenAI API Keys

Here we'll explain the different ways that are usually used to set the
API key up in RStudio :

-   By default, API calls will look for `OPENAI_API_KEY` environment
    variable. If you want to set a global environment variable, you can
    use the following command, where `"<APIKEY>"` should be replaced
    with your actual key:

::: cell
``` {.r .cell-code}
Sys.setenv(OPENAI_API_KEY = "<OPENAI_API_KEY>")
```
:::

-   Alternatively, you can set the key in your `.Renviron` file.

The following commands will open .Renviron for editing:

::: cell
``` {.r .cell-code}
require(usethis)
edit_r_environ()
```
:::

You can add the following line to the file (again, replace
`<my_api_key>` with your actual key):

::: cell
``` {.r .cell-code}
OPENAI_API_KEY=<my_api_key>
```
:::

Make sure your '.Renviron' ends with a newline. Don\'t forget to save
and restart R for this change to take effect. This now set the API key.
This is a less secure approach.

-   Use a more secure approach by using `ask_pass` that if possible uses
    `askpass::askpass()` to interactively safely prompt you for the
    values

    For that we'll be using this function.

    ::: cell
    ``` {.r .cell-code}
    set_api_key <- function(key = NULL) { 
      if (is.null(key)) { 
        key <- askpass::askpass("Please enter your API key") } 
      Sys.setenv("OPENAI_KEY" = key) }
    ```
    :::

httr2 provides `secret_encrypt()` and `secret_decrypt()` to scramble
secrets so that you can include them in your public source code without
worrying that others can read them. There are three basic steps to this
process:

1.  You create an **encryption** key with `secret_make_key()` that is
    used to scramble and descramble secrets using symmetric
    cryptography.

2.  You scramble your secrets with `secret_encrypt()` and store the
    resulting text directly in the source code of your package.

3.  When needed, you descramble the secret using `secret_decrypt().`

## Hands-on exercice using ChatGPT in R

Let's apply what we've learned so far.

Let's set up our token.

First let's run the `set_api_key( )`

::: cell
``` {.r .cell-code}
set_api_key <- function(key = NULL) { 
  if (is.null(key)) { 
    key <- askpass::askpass("Please enter your OpenAI API key") } 
  Sys.setenv("OPENAI_KEY" = key) }
```
:::

**`Sys.setenv`** sets environment variables. Then let's call the
function.

::: cell
``` {.r .cell-code}
set_api_key()
```
:::

You'll get a prompt asking for the token. For that, go to OpenAI and
click on "Create new secret key".

![](images/OpenAI_3.png)

Copy the API key and paste it in the prompt.

::: cell
``` {.r .cell-code}
key <- Sys.getenv("OPENAI_KEY")
is.null(key)
```
:::

We can pass our token to the OpenAI API through the query parameter
`api_key`, just like we did before.

::: cell
``` {.r .cell-code}
 library(httr) 
 key <- Sys.getenv("OPENAI_KEY") 
 headers = c( `Authorization` = paste('Bearer', key), `Content-Type` = 'application/json' ) 
res <- httr::GET(url = 'https://api.openai.com/v1/models/text-davinci-003', httr::add_headers(.headers=headers)) 
 res
```
:::

As we saw last time the status code is a three digit number that
summarises whether or not the request was successful.

::: cell
``` {.r .cell-code}
http_status(res)
```
:::

`GPT-3.5` models can understand and generate natural language or code.
`text-davinci-003` is Can do any language task with better quality,
longer output, and consistent instruction-following than the curie,
babbage, or ada models. Also supports inserting completions within text.
For more information about the different models go to this
[link](https://platform.openai.com/docs/models/gpt-3-5).

You can change the model. The most capable and cost effective model in
the GPT-3.5 family is **`gpt-3.5-turbo`** which has been optimized for
chat but works well for traditional completions tasks as well.

::: cell
``` {.r .cell-code}
library(httr) 
 key <- Sys.getenv("OPENAI_KEY") 
 headers = c( `Authorization` = paste('Bearer', key), `Content-Type` = 'application/json' ) 
res <- httr::GET(url = 'https://api.openai.com/v1/models/gpt-3.5-turbo', httr::add_headers(.headers=headers)) 
 res
```
:::

### POST requests

We use `POST` requests to send information (instead of retrieving it)
and create objects on the API's server.

When `POST()`ing, you can include data in the `body` of the request.
`httr` allows you to supply this in a number of different ways. The most
common way is a named list. If the body is a named list you should
specify how it will be encoded.

::: cell
``` {.r .cell-code}
# JSON encoded
r <- POST(url, body = body, encode = "json")
```
:::

Let's say you want to optimize your R code.

::: cell
``` {.r .cell-code}
code = "i <- 10\nwhile (i > 0) {\n i <- i - 1\n print(i)\n}" 
cat(code,"\n")
```
:::

In the prompt you have to ask

::: cell
``` {.r .cell-code}
prompt <- paste0('Optimize the following R code: "', code, '"') 
prompt
```
:::

You have to give the body of the POST request.

::: cell
``` {.r .cell-code}
params <- list(
model = "text-davinci-003", max_tokens = 256, temperature =0.7, top_p = 1, frequency_penalty = 0, presence_penalty = 0)
```
:::

Let's run our POST request

::: cell
``` {.r .cell-code}
r = content(POST( "https://api.openai.com/v1/completions", add_headers("Authorization" = paste('Bearer', key)), content_type_json(), body = toJSON(c(params, list(prompt = prompt)), auto_unbox = TRUE) ))
cat(r$choices[[1]]$text,"\n")
```
:::

## Exercice

Use the example above to do the same task as above using `httr2` .

### ChatGPT R packages

https://github.com/jcrodriguez1989/chatgpt

https://jameshwade.github.io/gpttools/

https://irudnyts.github.io/openai/

## References

https://info201.github.io/apis.html

https://observablehq.com/@periscopic/cozy-collecting-part-1

https://observablehq.com/@periscopic/cozy-collecting-part-2

https://observablehq.com/@periscopic/cozy-collecting-case-study
