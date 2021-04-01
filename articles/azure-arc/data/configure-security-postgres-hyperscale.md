---
title: Konfigurace zabezpečení pro skupinu serverů PostgreSQL Hyperscale s podporou služby Azure Arc
description: Konfigurace zabezpečení pro skupinu serverů PostgreSQL Hyperscale s podporou služby Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d6e27fddceb69efbb2c1697c09ee9b61d7f38ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687970"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Konfigurace zabezpečení pro skupinu serverů PostgreSQL Hyperscale s podporou služby Azure Arc

Tento dokument popisuje různé aspekty týkající se zabezpečení skupiny serverů:
- Šifrování neaktivních uložených dat
- Správa uživatelů
   - Obecné perspektivy
   - Změna hesla _Postgres_ administrativního uživatele
- Auditování

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Šifrování neaktivních uložených dat
Šifrování můžete v klidovém prostředí implementovat šifrováním disků, na které ukládáte své databáze, nebo pomocí databázových funkcí, které zašifrují data, která vkládáte nebo aktualizujete.

### <a name="hardware-linux-host-volume-encryption"></a>Hardware: šifrování svazku hostitele Linux
Implementací systémového šifrování dat zabezpečíte všechna data, která se nacházejí na discích používaných nastavením Azure ARC povoleno Data Services. Další informace o tomto tématu najdete v článku:
- [Šifrování dat v klidovém umístění](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) v systému Linux obecně 
- Šifrování disku s LUKS `cryptsetup` šifrovaným příkazem (Linux) ( https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) konkrétně od povoleného Arc Azure Data Services běží na fyzické infrastruktuře, kterou zadáte, budete mít na starosti zabezpečení infrastruktury.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Software: použití rozšíření PostgreSQL `pgcrypto` ve skupině serverů
Kromě šifrování disků, které se používají k hostování instalace Azure ARC, můžete nakonfigurovat skupinu serverů s podporou PostgreSQL ARC, aby vystavila mechanismy, které můžou vaše aplikace používat k šifrování dat ve vašich databázích. `pgcrypto`Rozšíření je součástí `contrib` rozšíření Postgres a je k dispozici ve skupině serverů PostgreSQL s podporou škálování Azure. Podrobnosti o `pgcrypto` rozšíření najdete [tady](https://www.postgresql.org/docs/current/pgcrypto.html).
V části Souhrn můžete pomocí následujících příkazů povolit rozšíření, vytvořit ho a použít ho:


#### <a name="create-the-pgcrypto-extension"></a>Vytvoření `pgcrypto` rozšíření
Připojte se ke skupině serverů pomocí nástroje klienta podle vašeho výběru a spusťte standardní dotaz PostgreSQL:
```console
CREATE EXTENSION pgcrypto;
```

> [Tady](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) najdete podrobnosti o tom, jak se připojit.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Ověřte seznam rozšíření připravených k použití ve skupině serverů.
Můžete ověřit, že `pgcrypto` je rozšíření připravené k použití, a to tak, že vydáte rozšíření dostupná ve skupině serverů.
Připojte se ke skupině serverů pomocí nástroje klienta podle vašeho výběru a spusťte standardní dotaz PostgreSQL:
```console
select * from pg_extension;
```
Měli byste vidět, `pgcrypto` jestli jste ho povolili a vytvořili s příkazy uvedenými nahoře.

#### <a name="use-the-pgcrypto-extension"></a>Použít `pgcrypto` rozšíření
Nyní můžete upravit kód aplikace tak, aby používaly kteroukoli z funkcí, které nabízí `pgcrypto` :
- Obecné funkce pro vytváření hodnot hash
- Funkce hash hesla
- Funkce šifrování PGP
- Nezpracované šifrovací funkce
- Funkce s náhodnými daty

Například pro generování hodnot hash. Spusťte příkaz:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

Vrátí následující hodnotu hash:

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Nebo například:

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

vrátí následující hodnotu hash:

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Nebo například pro ukládání šifrovaných dat, jako je heslo:

Řekněme, že moje aplikace ukládá tajné klíče do následující tabulky:

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

A při vytváření uživatele šifrují svoje heslo:

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

Teď vidíte, že moje heslo je zašifrované:

```console
select * from mysecrets;
```

Výstup:

- ID uživatele: 1
- Uživatelské jméno: Já
- USERpassword: $1 $ Uc7jzZOp $ NTfcGo7F10zGOkXOwjHy31

Když se připojím k aplikaci a předáte heslo, vyhledá se v `mysecrets` tabulce a vrátí jméno uživatele, pokud existuje shoda mezi heslem poskytnutým aplikací a hesly uloženými v tabulce. Například:

- Předáte chybné heslo:
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Výstup 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- Předáte správné heslo:

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   Výstup:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

Tento malý příklad ukazuje, že můžete data v klidovém stavu zašifrovat (ukládat zašifrovaná data) v PostgreSQL s povoleným rozšířením Azure ARC pomocí `pgcrypto` rozšíření Postgres a vaše aplikace můžou používat funkce nabízené nástrojem `pgcrypto` k manipulaci s těmito šifrovanými daty.

## <a name="user-management"></a>Správa uživatelů
### <a name="general-perspectives"></a>Obecné perspektivy
K vytvoření uživatelů nebo rolí můžete použít standardní Postgres způsob. Tyto artefakty však budou dostupné pouze na koordinačním uzlu. Ve verzi Preview tito uživatelé a tyto role ještě nebudou mít přístup k datům distribuovaným mimo koordinační uzel a na pracovních uzlech vaší skupiny serverů. Důvodem je to, že ve verzi Preview se definice uživatele nereplikuje na pracovní uzly.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>Změna hesla _Postgres_ administrativního uživatele
PostgreSQL s povoleným rozšířením Azure ARC přináší standardní Postgres administrativního uživatele _Postgres_ , pro který nastavíte heslo při vytváření skupiny serverů.
Obecný formát příkazu pro změnu hesla:
```console
azdata arc postgres server edit --name <server group name> --admin-password
```

Kde `--admin-password` je logická hodnota, která se vztahuje k přítomnosti hodnoty v proměnné prostředí AZDATA_PASSWORD **relace** .
Pokud proměnná prostředí AZDATA_PASSWORD **relace** existuje a má hodnotu, při spuštění výše uvedeného příkazu se nastaví heslo uživatele Postgres na hodnotu této proměnné prostředí.

Pokud proměnná prostředí AZDATA_PASSWORD **relace** existuje, ale nemá hodnotu, nebo proměnná prostředí AZDATA_PASSWORD **relace** neexistuje, vyzve uživatele k zadání hesla interaktivně.

#### <a name="change-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>Interaktivním způsobem změňte heslo Postgres administrativního uživatele.

1. Odstraňte proměnnou prostředí AZDATA_PASSWORD **relace** nebo odstraňte její hodnotu.
2. Spusťte příkaz:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Například
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   Zobrazí se výzva k zadání hesla a jeho potvrzení:
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   Při aktualizaci hesla výstup příkazu zobrazuje:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="change-the-password-of-the-postgres-administrative-user-using-the-azdata_password-session-environment-variable"></a>Změňte heslo administrativního uživatele Postgres pomocí proměnné prostředí AZDATA_PASSWORD **relace** :
1. Nastavte hodnotu proměnné prostředí AZDATA_PASSWORD **relace** na to, co chcete zadat jako heslo.
2. Spusťte příkaz:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Například
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   
   Při aktualizaci hesla výstup příkazu zobrazuje:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> Chcete-li ověřit, zda existuje proměnná prostředí relace AZDATA_PASSWORD a jaká hodnota má, spusťte příkaz:
> - V klientovi se systémem Linux:
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - Na klientovi s Windows pomocí PowerShellu:
> ```console
> echo $env:AZDATA_PASSWORD
> ```

## <a name="audit"></a>Auditování

V případě scénářů auditu Nakonfigurujte skupinu serverů tak, aby používala `pgaudit` rozšíření Postgres. Další podrobnosti `pgaudit` najdete v tématu [ `pgAudit` projekt GitHubu](https://github.com/pgaudit/pgaudit/blob/master/README.md). Pokud chcete povolit `pgaudit` rozšíření ve skupině serverů, [použijte rozšíření PostgreSQL](using-extensions-in-postgresql-hyperscale-server-group.md).


## <a name="next-steps"></a>Další kroky
- Zobrazit [ `pgcrypto` rozšíření](https://www.postgresql.org/docs/current/pgcrypto.html)
- Viz [použití rozšíření PostgreSQL](using-extensions-in-postgresql-hyperscale-server-group.md)

