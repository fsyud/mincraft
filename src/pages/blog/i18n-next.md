---
title: "i18n-next"
layout: "../../layouts/Blog.astro"
---

# nextjs i18n 实现原理

在 Next.js 中，i18n（国际化）的实现原理基于框架内的多语言支持功能。Next.js 从 10.x 版本开始原生支持国际化，这使得多语言路由、翻译文件管理、动态内容加载等更加高效。下面详细解释 Next.js 中 i18n 的实现原理及其工作机制：

## 1. Next.js 的内置 i18n 支持

Next.js 的国际化功能通过配置文件 next.config.js 中的 i18n 字段实现，主要用于指定支持的语言、默认语言、域名映射等。Next.js 自动处理根据用户语言的路由跳转和静态页面的语言切换。

示例：next.config.js 配置

```js
// next.config.js
module.exports = {
  i18n: {
    locales: ["en", "fr", "de"], // 支持的语言列表
    defaultLocale: "en", // 默认语言
    localeDetection: true, // 是否根据用户偏好检测语言
  },
};
```

locales：定义支持的语言列表。
defaultLocale：设置应用的默认语言，当没有明确指定语言时使用。
localeDetection：自动检测用户的浏览器语言，并根据语言设置跳转。

## 2. 路由国际化

Next.js 的国际化特性支持自动地为每个语言生成不同的 URL 路径。这意味着每个页面会为每种语言生成独立的静态页面。例如：

/about 是默认语言（如 en）的页面。
/fr/about 是法语版本的页面。
/de/about 是德语版本的页面。

实现原理：

路径前缀：Next.js 会根据 i18n 配置自动为不同语言添加前缀（例如 /fr、/de 等），然后将对应的翻译内容加载到页面中。
语言检测：通过浏览器设置或 URL 前缀来检测用户语言，并将用户定向到适合的语言版本。
例如：

访问 /about 时，Next.js 会根据浏览器语言（如果启用 localeDetection），或根据 defaultLocale 跳转到适合的语言页面。
如果启用了多个域名，Next.js 还可以根据域名映射到不同的语言。

## 3. 翻译文件的管理

Next.js 并不强制使用某种特定的 i18n 库来管理翻译文件，但常用的库是 i18next 或 react-intl。这些库配合 Next.js 的 i18n 路由功能可以高效地进行国际化管理。

翻译文件结构
通常，每个语言都有一个独立的翻译文件，这些文件通常存储在 public 文件夹或项目中的某个模块文件夹里。

示例：翻译文件

```js
// /locales/en/common.json
{
  "welcome": "Welcome",
  "logout": "Logout"
}

// /locales/fr/common.json
{
  "welcome": "Bienvenue",
  "logout": "Déconnexion"
}
```

在页面或组件中，你可以根据当前语言选择对应的翻译文本。

## 4. 语言切换机制

Next.js 的 i18n 通过路由前缀和 URL 实现语言切换。可以在应用中添加语言切换按钮，调用 Next.js 提供的路由 API 来实现语言跳转。

使用 next/link 实现语言切换：

```js
import Link from "next/link";
import { useRouter } from "next/router";

const LanguageSwitcher = () => {
  const router = useRouter();
  const { locale, locales } = router;

  return (
    <div>
      <p>Current locale: {locale}</p>
      <ul>
        {locales.map((loc) => (
          <li key={loc}>
            <Link href={router.asPath} locale={loc}>
              {loc}
            </Link>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default LanguageSwitcher;
```

上面的代码中，useRouter 提供了当前语言（locale）、支持的语言列表（locales）等信息，点击不同的链接即可切换语言。

## 5.国际化数据获取 (getStaticProps 和 getServerSideProps)

Next.js 中的静态生成和服务端渲染（如 getStaticProps 和 getServerSideProps）在国际化中起到了关键作用，帮助加载特定语言的内容。使用这些方法时，Next.js 会为每个语言的页面调用一次数据获取方法。

getStaticProps 示例：

```js
export async function getStaticProps({ locale }) {
  const translations = await import(`../locales/${locale}/common.json`);

  return {
    props: {
      translations,
    },
  };
}
```

在这个例子中，locale 是当前语言，Next.js 会根据 locale 值自动加载对应的翻译文件。

## 6.动态加载与按需加载

Next.js 支持按需加载翻译文件，避免所有翻译数据在页面初次加载时被加载，提升性能。例如，当用户切换到新的语言时，只加载所需的语言文件。

动态加载示例：

```js
import { useRouter } from "next/router";
import { useEffect, useState } from "react";

const Page = () => {
  const { locale } = useRouter();
  const [translations, setTranslations] = useState(null);

  useEffect(() => {
    const loadTranslations = async () => {
      const translations = await import(`../locales/${locale}/common.json`);
      setTranslations(translations);
    };

    loadTranslations();
  }, [locale]);

  if (!translations) {
    return <p>Loading...</p>;
  }

  return <div>{translations.welcome}</div>;
};

export default Page;
```

在这个例子中，每当 locale 变化时，Next.js 会异步加载对应的翻译文件，而不是在初次加载时将所有语言文件加载到页面中。

## 7.服务端渲染与静态生成

Next.js 支持在服务端渲染或静态生成时处理多语言内容。例如，在使用 getStaticPaths 时，可以为不同语言生成多个版本的静态页面。

示例：getStaticPaths 和 getStaticProps

```js
export async function getStaticPaths() {
  return {
    paths: [
      { params: { slug: "about" }, locale: "en" },
      { params: { slug: "about" }, locale: "fr" },
    ],
    fallback: false,
  };
}

export async function getStaticProps({ params, locale }) {
  const translations = await import(`../locales/${locale}/common.json`);

  return {
    props: {
      translations,
      slug: params.slug,
    },
  };
}
```

这段代码为每个支持的语言生成静态页面，例如 /en/about 和 /fr/about，并根据 locale 加载相应的翻译内容。

## 8.回退机制

Next.js i18n 支持语言的回退机制。如果用户选择的语言在某些页面没有提供完整的翻译，系统会回退到默认语言以保证页面可用性。

总结
Next.js 的 i18n 实现原理包括通过配置 next.config.js 来管理多语言支持、基于动态路由的语言切换、以及利用 getStaticProps 和 getStaticPaths 等机制为不同语言生成静态页面。开发者可以通过使用翻译文件、按需加载以及路由的语言前缀来高效地实现国际化。
