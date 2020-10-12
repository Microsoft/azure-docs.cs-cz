---
title: Instalace MongoDB na virtuální počítač s Windows v Azure
description: Naučte se instalovat MongoDB na virtuálním počítači Azure s Windows Serverem 2012 R2 vytvořeným pomocí modelu nasazení Správce prostředků.
documentationcenter: ''
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: d02fa8fa23b587db06f3d2d1e08f0a8565471123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510415"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalace a konfigurace MongoDB na virtuálním počítači s Windows v Azure
[MongoDB](https://www.mongodb.org) je oblíbená Open Source vysoce výkonná databáze NoSQL. Tento článek vás provede instalací a konfigurací MongoDB na virtuálním počítači s Windows serverem 2016 v Azure. MongoDB můžete také [nainstalovat do virtuálního počítače se systémem Linux v Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Požadavky
Než nainstalujete a nakonfigurujete MongoDB, musíte vytvořit virtuální počítač a v ideálním případě do něj přidat datový disk. Pokud chcete vytvořit virtuální počítač a přidat datový disk, přečtěte si následující články:

* Vytvořte virtuální počítač s Windows serverem pomocí [Azure Portal](quick-create-portal.md) nebo [Azure PowerShell](quick-create-powershell.md).
* Připojte datový disk k virtuálnímu počítači s Windows serverem pomocí [Azure Portal](attach-managed-disk-portal.md) nebo [Azure PowerShell](attach-disk-ps.md).

Pokud chcete začít s instalací a konfigurací MongoDB, [Přihlaste se k virtuálnímu počítači s Windows serverem](connect-logon.md) pomocí vzdálené plochy.

## <a name="install-mongodb"></a>Instalace MongoDB
> [!IMPORTANT]
> Funkce zabezpečení MongoDB, jako je například ověřování a vazba IP adres, nejsou ve výchozím nastavení povolené. Před nasazením MongoDB do produkčního prostředí by se měly povolit funkce zabezpečení. Další informace najdete v tématu [zabezpečení a ověřování MongoDB](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Po připojení k VIRTUÁLNÍmu počítači pomocí vzdálené plochy otevřete Internet Explorer z hlavního panelu.
2. Vyberte možnost **použít Doporučené zabezpečení, ochranu osobních údajů a nastavení kompatibility** při prvním otevření aplikace Internet Explorer a klikněte na tlačítko **OK**.
3. Konfigurace rozšířeného zabezpečení aplikace Internet Explorer je ve výchozím nastavení povolena. Přidejte web MongoDB do seznamu povolených webů:

   * Vyberte ikonu **nástroje** v pravém horním rohu.
   * V **Možnosti Internet**vyberte kartu **zabezpečení** a pak vyberte ikonu **Důvěryhodné servery** .
   * Klikněte na tlačítko **weby** . Přidejte *https:// \* . MongoDB.com* do seznamu důvěryhodných webů a potom dialogové okno zavřete.

     ![Konfigurovat nastavení zabezpečení aplikace Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Přejděte na stránku [MongoDB-downloads](https://www.mongodb.com/downloads) ( https://www.mongodb.com/downloads) .
5. V případě potřeby vyberte edici **Community Server** a pak vyberte nejnovější aktuální stabilní verzi pro*Windows Server 2008 R2 64-bit a novější*. Pokud chcete instalační program stáhnout, klikněte na **Stáhnout (MSI)**.

    ![Stáhnout instalační program MongoDB](./media/install-mongodb/download-mongodb.png)

    Po dokončení stahování spusťte instalační program.
6. Přečtěte si a přijměte licenční smlouvu. Po zobrazení výzvy vyberte **Dokončit** instalaci.
7. V případě potřeby se můžete rozhodnout také nainstalovat kompas, grafické rozhraní pro MongoDB.
8. Na poslední obrazovce klikněte na **nainstalovat**.

## <a name="configure-the-vm-and-mongodb"></a>Konfigurace virtuálního počítače a MongoDB
1. V instalačním programu MongoDB nejsou aktualizovány proměnné cest. Bez umístění MongoDB `bin` v proměnné PATH musíte zadat úplnou cestu pokaždé, když použijete spustitelný soubor MongoDB. Chcete-li přidat umístění do proměnné Path:

   * Klikněte pravým tlačítkem myši na nabídku **Start** a vyberte možnost **systém**.
   * Klikněte na **Upřesnit nastavení systému**a pak klikněte na **proměnné prostředí**.
   * V části **systémové proměnné**vyberte **cesta**a pak klikněte na **Upravit**.

     ![Konfigurace proměnných cest](./media/install-mongodb/configure-path-variables.png)

     Přidejte cestu do `bin` složky MongoDB. MongoDB se obvykle instaluje do složky *C:\Program Files\MongoDB*. Ověřte cestu k instalaci na VIRTUÁLNÍm počítači. Následující příklad přidá výchozí umístění instalace MongoDB do `PATH` proměnné:

     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```

     > [!NOTE]
     > Nezapomeňte přidat úvodní středník (), který `;` označuje, že přidáváte umístění do `PATH` proměnné.

2. Vytvářejte MongoDB data a protokolovat adresáře na datovém disku. V nabídce **Start** vyberte **příkazový řádek**. V následujících příkladech se vytvoří adresáře na jednotce F:

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Spusťte instanci MongoDB pomocí následujícího příkazu, podle toho upravte cestu k vašim datům a adresářům protokolů:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    MongoDB může trvat několik minut, než se soubory deníku přidělí a začnou naslouchat připojení. Všechny zprávy protokolu jsou směrovány do souboru *F:\MongoLogs\mongolog.log* při `mongod.exe` spuštění serveru a přidělují soubory deníku.

   > [!NOTE]
   > Pokud je spuštěná instance MongoDB, zůstane příkazového řádku na tomto úkolu zaměřený. Nechte okno příkazového řádku otevřené a pokračujte v běhu MongoDB. Případně nainstalujte MongoDB jako službu, jak je popsáno v dalším kroku.

4. Pro robustnější prostředí MongoDB nainstalujte `mongod.exe` jako službu. Vytvoření služby znamená, že nemusíte ponechávat příkazový řádek spuštěný pokaždé, když chcete používat MongoDB. Vytvořte službu následujícím způsobem a podle potřeby upravte cestu k vašim datům a adresářům protokolů:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```

    Předchozí příkaz vytvoří službu s názvem MongoDB s popisem "Mongo DB". Jsou také zadány následující parametry:

   * `--dbpath`Možnost určuje umístění datového adresáře.
   * `--logpath`Možnost musí být použita k určení souboru protokolu, protože spuštěná služba neobsahuje příkazové okno pro zobrazení výstupu.
   * `--logappend`Možnost určuje, že restartování služby způsobí, že se výstup připojí k existujícímu souboru protokolu.

   Chcete-li spustit službu MongoDB, spusťte následující příkaz:

    ```
    net start MongoDB
    ```

    Další informace o vytvoření služby MongoDB najdete v tématu [Konfigurace služby systému Windows pro MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testování instance MongoDB
Když MongoDB běží jako jediná instance nebo je nainstalovaná jako služba, teď můžete začít vytvářet a používat své databáze. Chcete-li spustit prostředí pro správu MongoDB, otevřete další okno příkazového řádku v nabídce **Start** a zadejte následující příkaz:

```
mongo
```

Databáze můžete vypsat pomocí `db` příkazu. Vložte data následujícím způsobem:

```
db.foo.insert( { a : 1 } )
```

Vyhledejte data následujícím způsobem:

```
db.foo.find()
```

Výstup se podobá následujícímu příkladu:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

`mongo`Konzolu ukončete následujícím způsobem:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurace pravidel brány firewall a skupiny zabezpečení sítě
Teď, když je nainstalovaná a spuštěná MongoDB, otevřete v bráně Windows Firewall port, abyste se mohli vzdáleně připojit k MongoDB. Pokud chcete vytvořit nové příchozí pravidlo, které povolí port TCP 27017, otevřete příkazový řádek PowerShellu pro správu a zadejte tento příkaz:

```powershell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Pravidlo můžete vytvořit také pomocí nástroje **Windows Firewall s pokročilým zabezpečením** – Nástroj pro správu grafiky. Vytvořte nové příchozí pravidlo, které povolí port TCP 27017.

V případě potřeby vytvořte pravidlo skupiny zabezpečení sítě, které povolí přístup k MongoDB mimo stávající podsíť virtuální sítě Azure. Pravidla skupiny zabezpečení sítě můžete vytvořit pomocí [Azure Portal](nsg-quickstart-portal.md) nebo [Azure PowerShell](nsg-quickstart-powershell.md). Stejně jako u pravidel brány Windows Firewall povolte port TCP 27017 virtuálnímu síťovému rozhraní virtuálního počítače MongoDB.

> [!NOTE]
> Port TCP 27017 je výchozím portem, který používá MongoDB. Tento port můžete změnit pomocí `--port` parametru při spuštění `mongod.exe` ručně nebo ze služby. Pokud změníte port, nezapomeňte aktualizovat pravidla brány Windows Firewall a skupiny zabezpečení sítě v předchozích krocích.


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak nainstalovat a nakonfigurovat MongoDB na VIRTUÁLNÍm počítači s Windows. Přístup k MongoDB na VIRTUÁLNÍm počítači s Windows teď můžete získat pomocí pokročilých témat v [dokumentaci k MongoDB](https://docs.mongodb.com/manual/).

