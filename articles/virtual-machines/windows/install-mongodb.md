---
title: Instalace MongoDB na virtuálním počítači v Azure Windows | Dokumentace Microsoftu
description: Zjistěte, jak instalace MongoDB na Virtuálním počítači Azure s Windows serverem 2012 R2 vytvořeného pomocí modelu nasazení Resource Manageru.
services: virtual-machines-windows
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: zarhoads
ms.openlocfilehash: f5cc7677074d521551287625c105018708e351f6
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49467527"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalace a konfigurace MongoDB na virtuálním počítači s Windows v Azure
[MongoDB](http://www.mongodb.org) je Oblíbené open source a vysoce výkonnou databází NoSQL. Tento článek vás provede instalací a konfigurací MongoDB na virtuálním počítači Windows serveru 2016 (VM) v Azure. Můžete také [instalace MongoDB na virtuální počítač s Linuxem v Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Požadavky
Než nainstalujete a nakonfigurujete MongoDB, budete muset vytvořit virtuální počítač a v ideálním případě by k ní přidat datový disk. Naleznete v následujících článcích pro vytvoření virtuálního počítače a přidat datový disk:

* Vytvoření virtuálního počítače s Windows serverem pomocí [na webu Azure portal](quick-create-portal.md) nebo [prostředí Azure PowerShell](quick-create-powershell.md).
* Připojení datového disku k virtuálnímu počítači s Windows serverem pomocí [na webu Azure portal](attach-managed-disk-portal.md) nebo [prostředí Azure PowerShell](attach-disk-ps.md).

Chcete-li zahájit instalaci a konfiguraci MongoDB, [Přihlaste se k virtuální počítač s Windows serverem](connect-logon.md) pomocí vzdálené plochy.

## <a name="install-mongodb"></a>Instalace MongoDB
> [!IMPORTANT]
> MongoDB funkce zabezpečení, jako je například ověřování a vazbu adresy IP, nejsou povolené ve výchozím nastavení. Funkce zabezpečení musí být aktivována před nasazením do produkčního prostředí MongoDB. Další informace najdete v tématu [MongoDB zabezpečení a ověřování](http://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Po připojení k virtuálnímu počítači pomocí vzdálené plochy, spusťte aplikaci Internet Explorer z hlavního panelu.
2. Vyberte **použít doporučené nastavení zabezpečení, ochrany osobních údajů a kompatibility** při aplikace Internet Explorer nejprve se otevře a klikněte na tlačítko **OK**.
3. Ve výchozím nastavení je povolená konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Přidáte web MongoDB do seznamu povolených webů:
   
   * Vyberte **nástroje** ikonu v pravém horním rohu.
   * V **Možnosti Internetu**, vyberte **zabezpečení** kartu a potom vyberte **Důvěryhodné servery** ikonu.
   * Klikněte na tlačítko **lokality** tlačítko. Přidat *https://\*. mongodb.com* do seznamu důvěryhodných serverů a potom dialogové okno zavřete.
     
     ![Konfigurovat nastavení zabezpečení aplikace Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Přejděte [MongoDB – soubory ke stažení](http://www.mongodb.com/downloads) stránky (http://www.mongodb.com/downloads).
5. V případě potřeby vyberte **Community Server** edition a pak vyberte nejnovější aktuální stabilní verzi pro*systému Windows Server 2008 R2 64-bit a novější*. Stažení instalačního programu, klikněte na tlačítko **stahování (msi)**.
   
    ![Stažení instalačního programu MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Po dokončení stahování spusťte instalační program.
6. Přečtěte si a přijměte licenční smlouvu. Po zobrazení výzvy, vyberte **Complete** nainstalovat.
7. V případě potřeby můžete také nainstalovat Compass grafické rozhraní pro MongoDB.
8. Na poslední obrazovce klikněte na tlačítko **nainstalovat**.

## <a name="configure-the-vm-and-mongodb"></a>Konfigurace virtuálního počítače a MongoDB
1. Proměnné cest nejsou aktualizovány podle MongoDB Instalační služby. Bez MongoDB `bin` umístění ve vaší proměnné path, je třeba zadat úplnou cestu pokaždé, když použijete spustitelný soubor MongoDB. Chcete-li přidat umístění do proměnné path:
   
   * Klikněte pravým tlačítkem myši **Start** nabídky a vybereme **systému**.
   * Klikněte na tlačítko **Upřesnit nastavení systému**a potom klikněte na tlačítko **proměnné prostředí**.
   * V části **systémové proměnné**vyberte **cesta**a potom klikněte na tlačítko **upravit**.
     
     ![Nakonfigurujte proměnné cesty](./media/install-mongodb/configure-path-variables.png)
     
     Přidat cestu k vaší MongoDB `bin` složky. MongoDB je obvykle nainstalován v *C:\Program Files\MongoDB*. Ověření cesty instalace na virtuální počítač. Následující příklad přidá výchozí umístění pro instalace MongoDB `PATH` proměnné:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Přidejte středník před (`;`) k označení, že chcete přidat umístění pro vaše `PATH` proměnné.

2. Vytvoření adresáře protokolu a data MongoDB na datový disk. Z **Start** nabídce vyberte možnost **příkazový řádek**. Následující příklady vytvoření adresáře na disku F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Spustit instanci databáze MongoDB pomocí následujícího příkazu nastavení cesty k datům a odpovídajícím způsobem protokolování adresářů:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Může trvat několik minut, než MongoDB přidělení soubory deníku a spuštění naslouchání pro připojení. Všechny zprávy protokolu jsou směrované na *F:\MongoLogs\mongolog.log* soubor jako `mongod.exe` server spustí a přidělí soubory deníku.
   
   > [!NOTE]
   > Příkazový řádek zůstává zaměřují se na tato úloha je spuštěna MongoDB instance. Ponechte okno příkazového řádku otevřené, aby kontinuálně běžely, MongoDB. Nebo instalace MongoDB jako služba, jak je uvedeno v následujícím kroku.

4. Výkonnější prostředí MongoDB, nainstalujte `mongod.exe` jako služba. Vytvoření služby znamená, že nemusíte ponechte příkazový řádek s pokaždé, když chcete použít MongoDB. Vytvořte službu následujícím způsobem odpovídajícím způsobem nastavení cesty k adresářům dat a protokolů:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Předchozí příkaz vytvoří službu s názvem MongoDB, s popisem "Mongo DB". Jsou také zadat následující parametry:
   
   * `--dbpath` Určuje umístění adresáře data.
   * `--logpath` Parametr musí být použit k určení souboru protokolu, protože nemá spuštěnou službu příkazové okno k zobrazení výstupu.
   * `--logappend` Možnost určuje, že restartování služby způsobí, že výstupní má připojit k existujícímu souboru protokolu.
   
   Chcete-li spustit službu MongoDB, spusťte následující příkaz:
   
    ```
    net start MongoDB
    ```
   
    Další informace o vytváření služeb MongoDB, naleznete v tématu [konfigurování služby Windows pro MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testování instanci databáze MongoDB
S databází MongoDB spuštěná jako jedna instance nebo nainstalovat jako službu teď můžete začít vytváření a používání vašich databází. Pokud chcete spustit MongoDB shell pro správu, otevřete další okno příkazového řádku z **Start** nabídce a zadejte následující příkaz:

```
mongo  
```

Můžete vytvořit seznam databází s `db` příkazu. Vložte některá data následujícím způsobem:

```
db.foo.insert( { a : 1 } )
```

Prohledat data následujícím způsobem:

```
db.foo.find()
```

Výstup se podobá následujícímu příkladu:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Ukončení `mongo` konzole následujícím způsobem:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurace brány firewall a pravidla skupiny zabezpečení sítě
Teď, když MongoDB je nainstalovaná a spuštěná, otevření portu v bráně Windows Firewall, abyste se mohli vzdáleně připojit k MongoDB. Pokud chcete vytvořit nové pravidlo, které povolí TCP port 27017, otevřete Správce příkazový řádek Powershellu a zadejte následující příkaz:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Toto pravidlo můžete vytvořit také pomocí **brány Windows Firewall s pokročilým zabezpečením** nástroj pro správu grafického rozhraní. Vytvořte nové pravidlo, které povolí TCP port 27017.

V případě potřeby vytvořte pravidlo skupiny zabezpečení sítě pro povolení přístupu k MongoDB z mimo existující podsítě virtuální sítě Azure. Pravidla skupiny zabezpečení sítě můžete vytvořit pomocí [webu Azure portal](nsg-quickstart-portal.md) nebo [prostředí Azure PowerShell](nsg-quickstart-powershell.md). Stejně jako u pravidla brány Windows Firewall povolte 27017 port TCP pro virtuální síťové rozhraní virtuálního počítače MongoDB.

> [!NOTE]
> TCP port 27017 je výchozí port je používán MongoDB. Tento port můžete změnit pomocí `--port` parametr při spuštění `mongod.exe` ručně nebo ze služby. Pokud změníte port, ujistěte se, že se aktualizovat pravidla brány Windows Firewall a skupinu zabezpečení sítě v předchozích krocích.


## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak nainstalovat a nakonfigurovat MongoDB na virtuálním počítači Windows. Nyní máte přístup MongoDB na virtuálním počítači Windows, pomocí následujících Pokročilá témata v [dokumentace pro MongoDB](https://docs.mongodb.com/manual/).

