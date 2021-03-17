---
title: Použití Azure cache pro Redis s Rust
description: V tomto rychlém startu se naučíte pracovat s Azure cache pro Redis pomocí Rust.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 17f38d79b75179d7a54ca5ed1d20dff18d0a0363
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121095"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>Rychlý Start: použití mezipaměti Azure pro Redis s Rust

V tomto článku se dozvíte, jak používat [programovací jazyk Rust](https://www.rust-lang.org/) pro interakci s [Azure cache pro Redis](./cache-overview.md). Ukáže příklady běžně používaných datových struktur Redis, jako je například [String](https://redis.io/topics/data-types-intro#redis-strings), [hash](https://redis.io/topics/data-types-intro#redis-hashes), [list](https://redis.io/topics/data-types-intro#redis-lists) atd. použití knihovny [Redis-RS](https://github.com/mitsuhiko/redis-rs) pro Redis. Tento klient zpřístupňuje rozhraní API vysoké úrovně i nízké úrovně a oba tyto styly jsou v akci, a to s použitím ukázkového kódu prezentovaného v tomto článku.

## <a name="skip-to-the-code-on-github"></a>Přeskočit na kód na GitHubu

Pokud chcete přeskočit přímo na kód, přečtěte si [rychlý Start Rust](https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart/) na GitHubu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Rust](https://www.rust-lang.org/tools/install) (verze 1,39 nebo vyšší)
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Vytvoření mezipaměti Azure pro instanci Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Kontrola kódu (volitelné)

Pokud vás zajímá, jak kód funguje, můžete zkontrolovat následující fragmenty kódu. V opačném případě můžete bez obav přeskočit ke [spuštění aplikace](#run-the-application).

`connect`Funkce se používá k navázání připojení k Azure cache pro Redis. Očekává, že název hostitele a heslo (přístupový klíč) se předávají přes proměnné prostředí `REDIS_HOSTNAME` a v `REDIS_PASSWORD` uvedeném pořadí. Formát adresy URL připojení je `rediss://<username>:<password>@<hostname>` – mezipaměť Azure pro Redis přijímá pouze zabezpečená připojení s [TLS 1,2 jako minimální požadovaná verze](cache-remove-tls-10-11.md).

Volání [Redis:: Client:: Open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) provede základní ověření, zatímco [get_connection ()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection) skutečně iniciuje připojení – program se zastaví, pokud se připojení nepovede z nějakého důvodu, například při nesprávném heslu.

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

`basics`Funkce pokrývá příkazy [set](https://redis.io/commands/set), [Get](https://redis.io/commands/get)a [incr](https://redis.io/commands/incr) . Rozhraní API nižší úrovně se používá pro `SET` a `GET` , které nastavuje a načítá hodnotu pro klíč s názvem `foo` . `INCRBY`Příkaz se spustí s využitím vysoké úrovně rozhraní API, tj. [incr](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) zvyšuje hodnotu klíče (s názvem `counter` ) `2` následovaný voláním [Get](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get) k načtení.

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

Níže uvedený fragment kódu ukazuje funkčnost `HASH` struktury dat Redis. [HSET](https://redis.io/commands/hset) se vyvolají pomocí rozhraní API nízké úrovně pro ukládání informací ( `name` , `version` , `repo` ) o ovladačích Redis (klientů). Například podrobnosti ovladače Rust (ten, který se používá v tomto ukázkovém kódu!), jsou zachyceny ve formě [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) a poté předány do rozhraní API nízké úrovně. Pak se načte pomocí [HGETALL](https://redis.io/commands/hgetall).

`HSET` lze také provést pomocí rozhraní API vysoké úrovně pomocí [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) , která přijímá pole řazených kolekcí členů. [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) se pak spustí, aby se načetla hodnota pro jediný atribut ( `repo` v tomto případě).

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

V níže uvedené funkci vidíte, jak použít `LIST` datovou strukturu. [LPUSH](https://redis.io/commands/lpush) se spustí (s rozhraním API nízké úrovně) pro přidání položky do seznamu a metoda [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) na nejvyšší úrovni se používá k načtení ze seznamu. Pak metoda [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) slouží k přidání několika položek do seznamu, který se pak načte pomocí metody [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) nízké úrovně.

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

Tady vidíte některé `SET` operace. Metoda [Sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd) (rozhraní API na vysoké úrovni) se používá k přidání několika položek do `SET` pojmenovaného `users` . [SISMEMBER](https://redis.io/commands/hset) se pak spustí (rozhraní API nízké úrovně) a zkontroluje, jestli `user1` existuje. Nakonec se [smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) používá k načtení a iterování všech položek sady ve formě vektoru ([vec <String> ](https://doc.rust-lang.org/std/vec/struct.Vec.html)).

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

`sorted_set` funkce níže ukazuje strukturu řazené sady dat. [ZADD](https://redis.io/commands/zadd) je vyvolána (s rozhraním API nízké úrovně) pro přidání náhodného celočíselného skóre pro aktér ( `player-1` ). Dále metoda [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) (rozhraní API na vysoké úrovni) slouží k přidání dalších přehrávačů ( `player-2` do `player-5` ) a příslušných (náhodně vygenerovaných) skóre. Počet položek v seřazené sadě je popsaný pomocí [ZCARD](https://redis.io/commands/zcard) a používá se jako limit pro příkaz [ZRANGE](https://redis.io/commands/zrange) (vyvolaný s rozhraním API nízké úrovně) k vypsání přehrávačů se svými skóre ve vzestupném pořadí.

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
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
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>Spuštění aplikace

Aplikace přijímá připojení a přihlašovací údaje ve formě proměnných prostředí. 

1. Načtení **názvu hostitele** a **přístupových klíčů** (dostupných prostřednictvím přístupových kláves) pro instanci Azure Cache for Redis v [Azure Portal](https://portal.azure.com/).

1. Nastavte je na příslušné proměnné prostředí:

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. V okně terminálu přejděte do správné složky. Například:

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. V terminálu spusťte následující příkaz, kterým aplikaci spustíte.

    ```shell
    cargo run
    ```
    
    Zobrazí se výstup jako takový:
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    Pokud chcete spustit určitou funkci, zakomentujte další funkce ve `main` funkci:
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
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

V tomto rychlém startu jste zjistili, jak pomocí ovladače Rust pro Redis připojit a spustit operace v mezipaměti Azure pro Redis.

> [!div class="nextstepaction"]
> [Vytvořte jednoduchou webovou aplikaci v ASP.NET, která používá Azure cache pro Redis.](./cache-web-app-howto.md)
