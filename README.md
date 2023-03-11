# Lingva Translate

<img src="public/logo.svg" width="128" align="right">


Alternative front-end for Google Translate, serving as a Free and Open Source translator with over a hundred languages available



## Deployment

As *Lingva* is a [Next.js](https://nextjs.org/) project you can deploy your own instance anywhere Next is supported.

The only requirement is to set an environment variable called `NEXT_PUBLIC_SITE_DOMAIN` with the domain you're deploying the instance under. This is used for the canonical URL and the meta tags.

Optionally, there are other environment variables available:
+ `NEXT_PUBLIC_FORCE_DEFAULT_THEME`: Force a certain theme over the system preference set by the user. The accepted values are `light` and `dark`.
+ `NEXT_PUBLIC_DEFAULT_SOURCE_LANG`: Set an initial *source* language instead of the default `auto`.
+ `NEXT_PUBLIC_DEFAULT_TARGET_LANG`: Set an initial *target* language instead of the default `en`.

### Docker

An [official Docker image](https://hub.docker.com/r/thedaviddelta/lingva-translate) is available to ease the deployment using Compose, Kubernetes or similar technologies. Remember to also include the environment variables (simplified to `site_domain`, `force_default_theme`, `default_source_lang` and `default_target_lang`) when running the container.

#### Docker Compose:

```
version: '3'

services:

  lingva:
    container_name: lingva
    image: thedaviddelta/lingva-translate:latest
    restart: unless-stopped
    environment:
      - site_domain=lingva.ml
      - force_default_theme=light
      - default_source_lang=auto
      - default_target_lang=en
    ports:
      - 3000:3000
```

#### Docker Run

```bash
docker run -p 3000:3000 -e site_domain=lingva.ml -e force_default_theme=light -e default_source_lang=auto -e default_target_lang=en thedaviddelta/lingva-translate:latest
```

### Vercel

Another easy way is to use the Next.js creators' own platform, [Vercel](https://vercel.com/), where you can deploy it for free with the following button.

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/git/external?repository-url=https%3A%2F%2Fgithub.com%2Fthedaviddelta%2Flingva-translate%2Ftree%2Fmain&env=NEXT_PUBLIC_SITE_DOMAIN&envDescription=Your%20domain&utm_source=lingva-team&utm_campaign=oss)


## Instances

These are the currently known *Lingva* instances. Feel free to make a Pull Request including yours (please remember to add `[skip ci]` to the last commit).

| Domain                                                                      | Hosting                                   | SSL Provider                                                                                    |
|:---------------------------------------------------------------------------:|:-----------------------------------------:|:-----------------------------------------------------------------------------------------------:|
| [lingva.ml](https://lingva.ml/) (Official)                                  | [Vercel](https://vercel.com/)             | [Let's Encrypt](https://www.ssllabs.com/ssltest/analyze.html?d=lingva.ml)                       |
| [translate.igna.wtf](https://translate.igna.wtf/)                           | [Vercel](https://vercel.com/)             | [Let's Encrypt](https://www.ssllabs.com/ssltest/analyze.html?d=translate.igna.wtf)              |
| [translate.plausibility.cloud](https://translate.plausibility.cloud/)       | [Hetzner](https://hetzner.com/)           | [Let's Encrypt](https://www.ssllabs.com/ssltest/analyze.html?d=translate.plausibility.cloud)    |
| [lingva.lunar.icu](https://lingva.lunar.icu/)                               | [Lansol](https://lansol.de/)              | [Cloudflare](https://www.ssllabs.com/ssltest/analyze.html?d=lingva.lunar.icu)                   |
| [translate.projectsegfau.lt](https://translate.projectsegfau.lt/)           | Self-hosted                               | [Let's Encrypt](https://www.ssllabs.com/ssltest/analyze.html?d=translate.projectsegfau.lt)      |
| [translate.dr460nf1r3.org](https://translate.dr460nf1r3.org/)               | [Netcup](https://netcup.eu/)              | [Cloudflare](https://www.ssllabs.com/ssltest/analyze.html?d=translate.dr460nf1r3.org)           |
| [lingva.garudalinux.org](https://lingva.garudalinux.org/)                   | [Hetzner](https://hetzner.com/)           | [Cloudflare](https://www.ssllabs.com/ssltest/analyze.html?d=lingva.garudalinux.org)             |
| [translate.jae.fi](https://translate.jae.fi/)                               | Self-hosted                               | [Let's Encrypt](https://www.ssllabs.com/ssltest/analyze.html?d=translate.jae.fi)                |


## Public APIs

Nearly all the *Lingva* instances should supply a pair of public developer APIs: a RESTful one and a GraphQL one.

*Note: both APIs return the translation audio as a `Uint8Array` (served as `number[]` in JSON and `[Int]` in GraphQL) with the contents of the audio buffer.*

### REST API v1

+ GET `/api/v1/:source/:target/:query`
```typescript
{
    translation: string
    info?: TranslationInfo
}
```

+ GET `/api/v1/audio/:lang/:query`
```typescript
{
    audio: number[]
}
```

+ GET `/api/v1/languages/?:(source|target)`
```typescript
{
    languages: [
        {
            code: string,
            name: string
        }
    ]
}
```

In addition, every endpoint can return an error message with the following structure instead.
```typescript
{
    error: string
}
```

### GraphQL API

+ `/api/graphql`
```graphql
query {
    translation(source: String target: String query: String!) {
        source: {
            lang: {
                code: String!
                name: String!
            }
            text: String!
            audio: [Int]!
            detected: {
                code: String
                name: String
            }
            typo: String
            pronunciation: String
            definitions: {
                type: String
                list: {
                    definition: String
                    example: String
                    field: String
                    synonyms: [String]
                }
            }
            examples: [String]
            similar: [String]
        }
        target: {
            lang: {
                code: String!
                name: String!
            }
            text: String!
            audio: [Int]!
            pronunciation: String
            extraTranslations: {
                type: String
                list: {
                    word: String
                    article: String
                    frequency: Int
                    meanings: [String]
                }
            }
        }
    }
    audio(lang: String! query: String!) {
        lang: {
            code: String!
            name: String!
        }
        text: String!
        audio: [Int]!
    }
    languages(type: SOURCE|TARGET) {
        code: String!
        name: String!
    }
}
```


## Related projects

+ [Lingva Scraper](https://github.com/thedaviddelta/lingva-scraper) - Google Translate scraper built and maintained specifically for this project
+ [SimplyTranslate](https://codeberg.org/SimpleWeb/SimplyTranslate-Web) - Very simple translation front-end with multi-engine support
+ [LibreTranslate](https://github.com/LibreTranslate/LibreTranslate) - FOSS translation service that uses the open [Argos](https://github.com/argosopentech/argos-translate) engine
+ [Lentil for Android](https://github.com/yaxarat/lingvaandroid) - Unofficial native client for Android that uses Lingva's public API
+ [Arna Translate](https://github.com/MahanRahmati/translate) - Unofficial cross-platform native client that uses Lingva's public API
+ [Translate-UT](https://github.com/walking-octopus/translate-ut) - Unofficial native client for Ubuntu Touch that uses Lingva's public API




<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!


## License

[![](https://www.gnu.org/graphics/agplv3-with-text-162x68.png)](https://www.gnu.org/licenses/agpl-3.0.html)

This project is [GNU AGPLv3](./LICENSE) licensed.
