---
title: Použití Azure cache pro Redis s nástrojem přejít
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci v cestách, která používá Azure cache pro Redis.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 04b582b5ef31e61039c5513ea2a4aa60f1c638e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121333"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>Rychlý Start: použití mezipaměti Azure pro Redis s nástrojem přejít

V tomto článku se naučíte, jak vytvořit REST API v nástroji, které budou ukládat a načítat informace o uživatelích s datovou strukturou [hash](https://redis.io/topics/data-types-intro#redis-hashes) v [Azure cache pro Redis](./cache-overview.md). 

## <a name="skip-to-the-code-on-github"></a>Přeskočit na kód na GitHubu

Pokud chcete přeskočit přímo na kód, Projděte si [rychlý Start](https://github.com/Azure-Samples/azure-redis-cache-go-quickstart/) na GitHubu.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Přejít](https://golang.org/doc/install) (nejlépe verze 1,13 nebo vyšší)
- [Git](https://git-scm.com/downloads)
- Klient HTTP, jako je například [kudrlinkou](https://curl.se/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Vytvoření mezipaměti Azure pro instanci Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Kontrola kódu (volitelné)

Pokud vás zajímá, jak kód funguje, můžete zkontrolovat následující fragmenty kódu. V opačném případě můžete bez obav přeskočit ke [spuštění aplikace](#run-the-application).

Knihovna open source [Redis](https://github.com/go-redis/redis) se používá k interakci s Azure cache pro Redis.

`main`Funkce se spustí po načtení názvu hostitele a hesla (přístupová klávesa) pro instanci Azure cache pro Redis.

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

Pak navážeme připojení k Azure cache pro Redis. Všimněte si, že je používána [tls.Config](https://golang.org/pkg/crypto/tls/#Config) – mezipaměť Azure pro Redis přijímá pouze zabezpečená připojení s [TLS 1,2 jako minimální požadovaná verze](cache-remove-tls-10-11.md).

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

Pokud je připojení úspěšné, nakonfigurují se [obslužné rutiny HTTP](https://golang.org/pkg/net/http/#HandleFunc) na zpracování `POST` a `GET` operace a spustí se server http. 

> [!NOTE] 
> [Gorilla MUX Library](https://github.com/gorilla/mux) se používá ke směrování (i když není nezbytně nutné a můžeme se k tomu přibrat pomocí standardní knihovny pro tuto ukázkovou aplikaci).
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

`userHandler` struktura zapouzdřuje [Redis. Klient](https://pkg.go.dev/github.com/go-redis/redis/v8#Client), který je používán `createUser` `getUser` metodami, kód pro tyto metody nebyl zahrnut pro zkrácení. 

- `createUser`: přijímá datovou část JSON (obsahující informace o uživateli) a uloží ji jako `HASH` v mezipaměti Azure pro Redis.
- `getUser`: načte informace o uživateli z `HASH` nebo vrátí odpověď HTTP `404` , pokud nebyla nalezena.

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
```

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Začněte klonováním aplikace z GitHubu.

1. Otevřete příkazový řádek a vytvořte novou složku s názvem `git-samples` .

    ```bash
    md "C:\git-samples"
    ```

1. Otevřete okno terminálu Git, například git bash. Pomocí `cd` příkazu přejděte do nové složky, do které budete naklonovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

1. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>Spuštění aplikace

Aplikace přijímá připojení a přihlašovací údaje ve formě proměnných prostředí. 

1. Načtení **názvu hostitele** a **přístupových klíčů** (dostupných prostřednictvím přístupových kláves) pro instanci Azure Cache for Redis v [Azure Portal](https://portal.azure.com/)

1. Nastavte je na příslušné proměnné prostředí:

    ```shell
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. V okně terminálu přejděte do správné složky. Například:

    ```shell
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. V terminálu spusťte následující příkaz, kterým aplikaci spustíte.

    ```shell
    go run main.go
    ```

Server HTTP se spustí na portu `8080` .

## <a name="test-the-application"></a>Testování aplikace

1. Vytvořte několik uživatelských záznamů. Následující příklad používá oblé:

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. Načtěte stávajícího uživatele s jeho `id` :

    ```bash
    curl -i localhost:8080/users/1
    ```

    Odpověď JSON byste měli získat takto:
    
    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. Pokud se pokusíte načíst uživatele, který neexistuje, dostanete HTTP `404` . Například:

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste hotovi s prostředky a skupinami prostředků Azure, které jste vytvořili v tomto rychlém startu, můžete je odstranit, abyste se vyhnuli poplatkům.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků a všechny prostředky v ní se trvale odstraní. Pokud jste instanci Azure cache for Redis vytvořili ve stávající skupině prostředků, kterou chcete zachovat, můžete odstranit jenom mezipaměť tak, že na stránce s **přehledem** mezipaměti vyberete **Odstranit** . 

Odstranění skupiny prostředků a její Redis Cache pro instanci Azure:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **skupiny prostředků**.
1. Do textového pole **filtrovat podle názvu** zadejte název skupiny prostředků, která obsahuje vaši instanci mezipaměti, a pak ji vyberte z výsledků hledání. 
1. Na stránce skupiny prostředků vyberte **Odstranit skupinu prostředků**.
1. Zadejte název skupiny prostředků a pak vyberte **Odstranit**.
   
   ![Odstranění skupiny prostředků pro Azure cache pro Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak začít používat službu přejít s Azure cache pro Redis. Nakonfigurovali jste a spustili jednoduchou REST APIovou aplikaci, která umožňuje vytvářet a získávat informace o uživatelích s `HASH` datovou strukturou Redis.

> [!div class="nextstepaction"]
> [Vytvořte jednoduchou webovou aplikaci v ASP.NET, která používá Azure cache pro Redis.](./cache-web-app-howto.md)
