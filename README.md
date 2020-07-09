# Users API

API REST che simula un database di 5000 utenti.

Basata su [json-server].

## Istruzioni

Per avviare il server, eseguire il comando:

```shell
npm start
```

Il server è configurato in modo da impedire la modifica dei dati. Per modificare
questa o altre impostazioni, fare riferimento alla documentazione ufficiale di
[json-server].

In particolare, è possibile modificare la porta su cui è in ascolto il server
utilizzando il parametro di configurazione `port` (già presente nel file di
configurazione [`json-server.json`](./json-server.json)).

> ## Origine dei dati
> 
> I dati provengono da [randomuser.me].
> 
> Dal momento che tali dati non possiedono la proprietà `id` necessaria a
> [json-server] per funzionare, sono stati modificati utilizzando questo
> middleware:
> 
> ```js
> // mw.js
> module.exports = (req, res, next) => {
>   var originalSend = res.send;
>   res.send = function(){
>     const [payload, ...rest] = [...arguments];
>     const data = JSON.parse(payload);
>     const fixedData = data.map((data, i) => ({
>       ...data,
>       id: i + 1
>     }));
>     originalSend.apply(res, [
>       JSON.stringify(fixedData),
>       ...rest
>     ]);
>   };
>   next();
> }
> ```
> 
> Il middleware è stato registrato in [`json-server.json`](./json-server.json):
> 
> ```json
> {
>   "port": 3000,
>   "read-only": true,
>   "delay": 30,
>   "watch": true,
>   "middlewares": [
>     "./mw.js"
>   ]
> }
> ```
> 
> Quindi il server è stato avviato sul file contenente i dati originali
> [`randomusers.json`](./randomusers.json):
> 
> ```shell
> json-server randomusers.json
> ```
> 
> Una volta invocato l'endpoint `/users`, i dati modificati degli utenti (in cui
> l'oggetto `id` è stato sostituito dall'`id` generato nel middleware) sono
> stati salvati nel file [`db.json`](./db.json) attualmente utilizzato.

[json-server]: https://github.com/typicode/json-server
[randomuser.me]: https://randomuser.me/
