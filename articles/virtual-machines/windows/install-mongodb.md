---
title: Nainstalujte MongoDB v systému Windows virtuálního počítače v Azure | Microsoft Docs
description: Naučte se nainstalovat na virtuální počítač Azure s Windows serverem 2012 R2 vytvořené pomocí modelu nasazení Resource Manager MongoDB.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: f3fe9751467a1fc34f4e9d02855c4aff307424a3
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2017
ms.locfileid: "26745975"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalace a konfigurace MongoDB na virtuální počítač s Windows v Azure
[MongoDB](http://www.mongodb.org) je Oblíbené databáze NoSQL open source a vysoce výkonné. Tento článek vás provede instalací a konfigurací MongoDB na Windows Server 2016 virtuálního počítače (VM) v Azure. Můžete také [nainstalujte MongoDB na virtuální počítač s Linuxem v Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Požadavky
Než nainstalujete a nakonfigurujete MongoDB, musíte vytvořit virtuální počítač a v ideálním případě přidat datový disk do ní. Najdete v následujících článcích pro vytvoření virtuálního počítače a přidat datový disk:

* Vytvoření virtuálního počítače Windows serveru pomocí [portálu Azure](quick-create-portal.md) nebo [prostředí Azure PowerShell](quick-create-powershell.md).
* Připojit datový disk do virtuálního počítače Windows serveru pomocí [portálu Azure](attach-managed-disk-portal.md) nebo [prostředí Azure PowerShell](attach-disk-ps.md).

Zahájíte instalaci a konfiguraci MongoDB, [Přihlaste se k vaší virtuální počítač Windows serveru](connect-logon.md) pomocí vzdálené plochy.

## <a name="install-mongodb"></a>Instalace MongoDB
> [!IMPORTANT]
> Funkce zabezpečení MongoDB, jako je ověřování a vazbu IP adresy, nejsou povolené ve výchozím nastavení. Před nasazením MongoDB v produkčním prostředí by měl povolit funkce zabezpečení. Další informace najdete v tématu [MongoDB zabezpečení a ověřování](http://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Po připojení k virtuálnímu počítači pomocí vzdálené plochy, otevřete Internet Explorer z hlavního panelu.
2. Vyberte **doporučuje použít nastavení zabezpečení, ochrany osobních údajů a kompatibility** když Internet Explorer nejprve otevře a klikněte na tlačítko **OK**.
3. Konfigurace rozšířeného zabezpečení aplikace Internet Explorer je standardně povolená. Přidejte web MongoDB do seznamu povolených webů:
   
   * Vyberte **nástroje** ikonu v pravém horním rohu.
   * V **Možnosti Internetu**, vyberte **zabezpečení** a pak vyberte **Důvěryhodné servery** ikonu.
   * Klikněte **lokality** tlačítko. Přidat *https://\*. mongodb.com* do seznamu důvěryhodných serverů a pak zavřete dialogové okno.
     
     ![Konfigurovat nastavení zabezpečení aplikace Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Vyhledejte [MongoDB - stáhne](http://www.mongodb.com/downloads) stránky (http://www.mongodb.com/downloads).
5. V případě potřeby vyberte **komunity serveru** edition a potom vyberte nejnovější aktuální stabilní verzi pro*Windows Server 2008 R2 64-bit a novější*. Chcete-li stáhnout instalační program, klikněte na tlačítko **stahování (msi)**.
   
    ![Stažení instalačního programu MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Po dokončení stahování spusťte instalační program.
6. Přečtěte si a přijměte licenční smlouvu. Když se zobrazí výzva, vyberte **Complete** nainstalovat.
7. V případě potřeby můžete také nainstalovat kompas, grafické rozhraní pro MongoDB.
8. Na poslední obrazovka, klikněte na tlačítko **nainstalovat**.

## <a name="configure-the-vm-and-mongodb"></a>Konfigurace virtuálního počítače a MongoDB
1. Proměnné cest nejsou aktualizovat instalační program MongoDB. Bez MongoDB `bin` umístění vaše proměnná path, musíte zadat úplnou cestu pokaždé, když používáte spustitelný soubor MongoDB. Přidání umístění do vaše proměnná path:
   
   * Klikněte pravým tlačítkem myši **spustit** nabídce a vyberte **systému**.
   * Klikněte na tlačítko **Upřesnit nastavení systému**a potom klikněte na **proměnné prostředí**.
   * V části **systémové proměnné**, vyberte **cesta**a potom klikněte na **upravit**.
     
     ![Nakonfigurujte proměnné cest](./media/install-mongodb/configure-path-variables.png)
     
     Přidejte cestu k vaší MongoDB `bin` složky. MongoDB je obvykle nainstalovaný v *C:\Program Files\MongoDB*. Ověření cesty instalace na virtuální počítač. Následující příklad přidá výchozí umístění pro instalace MongoDB `PATH` proměnné:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Nezapomeňte přidat středník před (`;`) k označení, že přidáváte umístění, do kterého vaše `PATH` proměnné.

2. Vytváření adresářů MongoDB protokolu a data na datový disk. Z **spustit** nabídce vyberte možnost **příkazového řádku**. Následující příklady vytvořte na jednotce F: adresáře
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Spuštění MongoDB instance pomocí následujícího příkazu, úpravě cesty k datům a odpovídajícím způsobem protokolu adresáře:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    To může trvat několik minut, než MongoDB přidělit soubory deníku a zahájit naslouchání pro připojení. Všechny zprávy protokolu jsou směrované *F:\MongoLogs\mongolog.log* souboru jako `mongod.exe` serveru spustí a přiděluje soubory deníku.
   
   > [!NOTE]
   > Do příkazového řádku zůstává zaměřují se na tato úloha je spuštěna MongoDB instance. Nechte okno příkazového řádku otevřené pokračovat v provozu MongoDB. Nebo nainstalujte MongoDB jako služba, jak je podrobně uvedeno v dalším kroku.

4. Robustnější prostředí MongoDB, nainstalujte `mongod.exe` jako služba. Vytvoření služby znamená, že je nebudete muset ponechte příkazový řádek s pokaždé, když chcete použít MongoDB. Vytvoření služby následujícím způsobem, upraví odpovídajícím způsobem cestu do vašich adresářů protokolu a data:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Předchozí příkaz vytvoří službu s názvem MongoDB, s popisem "Mongo DB". Jsou také zadat následující parametry:
   
   * `--dbpath` Možnost určuje umístění do adresáře dat.
   * `--logpath` Možnost se musí použít k určení souboru protokolu, protože spuštěné služby nemá okno příkazového řádku k zobrazení výstupu.
   * `--logappend` Možnost určuje, že restartování služby způsobí, že výstupní má být připojen ke stávajícímu souboru protokolu.
   
   Spusťte službu MongoDB, spusťte následující příkaz:
   
    ```
    net start MongoDB
    ```
   
    Další informace o vytváření MongoDB služby najdete v tématu [konfigurace služby systému Windows pro MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testovací MongoDB instance
S MongoDB spuštěna jako jedna instance nebo nainstalovat jako službu můžete nyní spustit vytváření a používání vašich databází. Pokud chcete spustit prostředí pro správu MongoDB, otevřete další okno příkazového řádku z **spustit** nabídce a zadejte následující příkaz:

```
mongo  
```

Můžete vytvořit seznam databáze s `db` příkaz. Vložte některá data následujícím způsobem:

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

Ukončení `mongo` konzole následujícím způsobem:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurace pravidel skupin zabezpečení sítě a brány firewall
Teď, když MongoDB je nainstalovaná a spuštěná, aby mohli vzdáleně připojit k MongoDB otevřete port v bráně Windows Firewall. Pokud chcete vytvořit nové příchozí pravidlo, které povolí TCP port 27017, otevřete Správce příkazovém řádku prostředí PowerShell a zadejte následující příkaz:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Toto pravidlo můžete vytvořit také pomocí **brány Windows Firewall s pokročilým zabezpečením** nástroj pro správu grafického rozhraní. Vytvořte nové příchozí pravidlo, které povolí TCP port 27017.

V případě potřeby vytvořte skupinu zabezpečení sítě pravidlo povolující přístup k MongoDB z mimo existující podsíť virtuální sítě Azure. Skupina zabezpečení sítě pravidla můžete vytvořit pomocí [portál Azure](nsg-quickstart-portal.md) nebo [prostředí Azure PowerShell](nsg-quickstart-powershell.md). Stejně jako u pravidla brány Windows Firewall povolí port TCP 27017 k rozhraní virtuální sítě virtuálního počítače MongoDB.

> [!NOTE]
> TCP port 27017 je výchozí port je používán MongoDB. Tento port můžete změnit pomocí `--port` parametr při spouštění `mongod.exe` ručně nebo ze služby. Pokud změníte port, nezapomeňte aktualizovat pravidla brány Windows Firewall a skupinu zabezpečení sítě v předchozích krocích.


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak nainstalovat a nakonfigurovat MongoDB na vašem virtuálním počítači Windows. Nyní můžete k MongoDB na vašem virtuálním počítači Windows pomocí následujících Pokročilá témata v [dokumentace k MongoDB](https://docs.mongodb.com/manual/).

