---
title: Развёртывание
---

Приложения Sapper запускаются везде, где поддерживается работа Node 10 или выше.

### Развёртывание из sapper build

Для запуска собранного приложения необходимы папки `__sapper__/build` и `static`, а также установленные зависимости(кроме тех что нужны при разработке) в папке `node_modules`. Необходимые зависимости устанавливаются командой `npm ci --prod`, затем запустите приложение командой :

```bash
node __sapper__/build
```

### Развёртывание в Vercel

Можно воспользоваться сторонней библиотекой вроде [`vercel-sapper`](https://www.npmjs.com/package/vercel-sapper) для загрузки нашего проекта в [Vercel] ([ранее ZEIT Now](https://vercel.com/blog/zeit-is-now-vercel)). Прочтите [документацию](https://github.com/thgh/vercel-sapper#readme) для получения детальной информации по развёртыванию в [Vercel].


### Развёртывание в Now

> Этот раздел описывает работу только с Now 1, а не с Now 2

Мы легко можем развернуть приложение на платформе [Now][]:

```bash
npm install -g now
now
```

Эти команды загрузят исходный код в Now, после чего он самостоятельно выполнит `npm run build` и `npm start` и даст вам URL, по которому будет располагаться развёрнутое приложение.

Для других хостингов вам скорее всего нужно будет выполнять `npm run build` вручную.


### Развёртывание сервис-воркеров

Sapper обеспечивает уникальность файла сервис-воркера(`service-worker.js`), путём добавления временной метки в исходный код, которая рассчитывается с использованием функции `Date.now ()`.

В окружениях, где приложение разворачивается на нескольких физических серверах (например, [Now][]), следует использовать одинаковую временную метку для сервис-воркеров на всех инстансах. В противном случае пользователи могут столкнуться с проблемами, когда сервис-воркер будет неожиданно обновляться, потому что приложение обращается сначала к серверу 1, затем к серверу 2, а метка времени на них будет различаться.

Чтобы переопределить метку времени Sapper, вы можете использовать переменную среды (например, `SAPPER_TIMESTAMP`), а затем изменить `service-worker.js` подобным образом:

```js
const timestamp = process.env.SAPPER_TIMESTAMP; // вместо `import { timestamp }`

const ASSETS = `cache${timestamp}`;

export default {
	/* ... */
	plugins: [
		/* ... */
		replace({
			/* ... */
			'process.env.SAPPER_TIMESTAMP': process.env.SAPPER_TIMESTAMP || Date.now()
		})
	]
}
```

Затем вы можете определить эту переменную при запуске, например:

```bash
SAPPER_TIMESTAMP=$(date +%s%3N) npm run build
```

При развертовании на [Now][], вы можете передать эту переменную непосредственно в Now:

```bash
now -e SAPPER_TIMESTAMP=$(date +%s%3N)
```

[Now]: https://zeit.co/now