---
title: Instalace MongoDB na virtuální počítač s Windows v Azure
description: Zjistěte, jak nainstalovat MongoDB na virtuální počítač Azure se systémem Windows Server 2012 R2 vytvořeným pomocí modelu nasazení Správce prostředků.
documentationcenter: ''
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: a5ba7d7fce3f3eabd223956ca8d9cc824fbd0c5f
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869456"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalace a konfigurace MongoDB na virtuálním počítači s Windows v Azure
[MongoDB](https://www.mongodb.org) je populární open-source, vysoce výkonná NoSQL databáze. Tento článek vás provede instalací a konfigurací MongoDB na virtuálním počítači (VM) Windows Serveru 2016 v Azure. [MongoDB](../linux/install-mongodb.md)můžete taky nainstalovat na virtuální počítač s Linuxem v Azure .

## <a name="prerequisites"></a>Požadované součásti
Před instalací a konfigurací MongoDB je potřeba vytvořit virtuální počítač a v ideálním případě do něj přidat datový disk. V následujících článcích můžete vytvořit virtuální počítače a přidat datový disk:

* Vytvořte virtuální počítač s Windows Server pomocí [portálu Azure nebo](quick-create-portal.md) [Azure PowerShellu](quick-create-powershell.md).
* Připojení datového disku k virtuálnímu počítači windows serveru pomocí [portálu Azure nebo](attach-managed-disk-portal.md) [Azure PowerShellu](attach-disk-ps.md).

Chcete-li zahájit instalaci a konfiguraci mongoDB, [přihlaste se k virtuálnímu počítači se systémem Windows Server](connect-logon.md) pomocí vzdálené plochy.

## <a name="install-mongodb"></a>Instalace MongoDB
> [!IMPORTANT]
> Funkce zabezpečení MongoDB, jako je ověřování a vazba ip adres, nejsou ve výchozím nastavení povoleny. Funkce zabezpečení by měly být povoleny před nasazením MongoDB do produkčního prostředí. Další informace naleznete [v tématu MongoDB Security and Authentication](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Po připojení k virtuálnímu počítači pomocí vzdálené plochy otevřete Internet Explorer z hlavního panelu.
2. Vyberte Při prvním otevření aplikace Internet Explorer **možnost Použít doporučené nastavení zabezpečení, ochrany osobních údajů a kompatibility** a klepněte na tlačítko **OK**.
3. Konfigurace rozšířeného zabezpečení aplikace Internet Explorer je ve výchozím nastavení povolena. Přidejte web MongoDB do seznamu povolených webů:
   
   * V pravém horním rohu vyberte ikonu **Nástroje.**
   * V **části Možnosti Internetu**vyberte kartu **Zabezpečení** a pak vyberte ikonu **Důvěryhodné servery.**
   * Klikněte na tlačítko **Weby.** Přidejte *\*https:// .mongodb.com* do seznamu důvěryhodných serverů a zavřete dialogové okno.
     
     ![Konfigurace nastavení zabezpečení aplikace Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Přejděte na stránku [MongoDB - Downloads](https://www.mongodb.com/downloads) (https://www.mongodb.com/downloads).
5. V případě potřeby vyberte edici **Community Server** a potom vyberte nejnovější aktuální stabilní verzi pro*64bitovou a novější verzi systému Windows Server 2008 R2*. Chcete-li instalační program stáhnout, klepněte na tlačítko **STÁHNOUT (msi).**
   
    ![Stáhnout instalační program MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Po dokončení stahování spusťte instalační program.
6. Přečtěte si a přijměte licenční smlouvu. Až se zobrazí výzva, vyberte **Dokončit** instalaci.
7. V případě potřeby můžete také nainstalovat Compass, grafické rozhraní pro MongoDB.
8. Na poslední obrazovce klikněte na **Instalovat**.

## <a name="configure-the-vm-and-mongodb"></a>Konfigurace virtuálního počítače a MongoDB
1. Proměnné cesty nejsou aktualizovány instalačním programem MongoDB. Bez umístění MongoDB `bin` v proměnné cesty je třeba zadat úplnou cestu při každém použití spustitelného souboru MongoDB. Přidání umístění do proměnné cesty:
   
   * Klepněte pravým tlačítkem myši na nabídku **Start** a vyberte **položku Systém**.
   * Klepněte na tlačítko **Upřesnit nastavení systému**a potom klepněte na **položku Proměnné prostředí**.
   * V části **Systémové proměnné**vyberte **Cestu**a klepněte na tlačítko **Upravit**.
     
     ![Konfigurace proměnných cesty](./media/install-mongodb/configure-path-variables.png)
     
     Přidejte cestu do složky `bin` MongoDB. MongoDB je obvykle nainstalován v *C:\Program Files\MongoDB*. Ověřte instalační cestu na virtuálním počítači. Následující příklad přidá výchozí umístění instalace MongoDB do `PATH` proměnné:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Nezapomeňte přidat úvodní středník`;`( ) označující, že přidáváte umístění do `PATH` proměnné.

2. Vytvořte data MongoDB a protokolujte adresáře na datovém disku. V nabídce **Start** vyberte **příkazový řádek**. Následující příklady vytvářejí adresáře na jednotce F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Spusťte instanci MongoDB pomocí následujícího příkazu a odpovídajícím způsobem upravte cestu k datům a protokolujte adresáře:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Může trvat několik minut pro MongoDB přidělit soubory deníku a začít naslouchat připojení. Všechny zprávy protokolu jsou směrovány do souboru *F:\MongoLogs\mongolog.log* při `mongod.exe` spuštění serveru a přiděluje soubory deníku.
   
   > [!NOTE]
   > Příkazový řádek zůstane zaměřen na tuto úlohu, zatímco je spuštěna instance MongoDB. Chcete-li pokračovat ve spuštění mongoDB, ponechejte okno příkazového řádku otevřené. Nebo nainstalujte MongoDB jako službu, jak je podrobně popsáno v dalším kroku.

4. Chcete-li vytvořit robustnější prostředí MongoDB, nainstalujte `mongod.exe` ji jako službu. Vytvoření služby znamená, že nemusíte ponechat příkazový řádek spuštěný pokaždé, když chcete použít MongoDB. Vytvořte službu následujícím způsobem a odpovídajícím způsobem upravte cestu k datům a protokolujte adresáře:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Předchozí příkaz vytvoří službu s názvem MongoDB s popisem "Mongo DB". Jsou také určeny následující parametry:
   
   * Tato `--dbpath` možnost určuje umístění datového adresáře.
   * Tato `--logpath` možnost musí být použita k určení souboru protokolu, protože spuštěná služba nemá příkazové okno pro zobrazení výstupu.
   * Tato `--logappend` možnost určuje, že restartování služby způsobí, že výstup připojit k existující soubor protokolu.
   
   Chcete-li spustit službu MongoDB, spusťte následující příkaz:
   
    ```
    net start MongoDB
    ```
   
    Další informace o vytvoření služby MongoDB naleznete v [tématu Konfigurace služby systému Windows pro mongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testování instance MongoDB
S MongoDB běží jako jedna instance nebo nainstalován jako služba, můžete nyní začít vytvářet a používat databáze. Chcete-li spustit prostředí pro správu MongoDB, otevřete z nabídky **Start** jiné okno příkazového řádku a zadejte následující příkaz:

```
mongo  
```

Databáze můžete vypsat `db` pomocí příkazu. Vložte některá data takto:

```
db.foo.insert( { a : 1 } )
```

Vyhledejte data takto:

```
db.foo.find()
```

Výstup se podobá následujícímu příkladu:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Konzoli `mongo` opusťte následujícím způsobem:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurace pravidel brány firewall a skupiny zabezpečení sítě
Nyní, když je mongoDB nainstalován a spuštěn, otevřete port v bráně Windows Firewall, abyste se mohli vzdáleně připojit k MongoDB. Chcete-li vytvořit nové příchozí pravidlo umožňující port TCP 27017, otevřete výzvu prostředí PowerShell pro správu a zadejte následující příkaz:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Pravidlo můžete také vytvořit pomocí grafického nástroje Windows **Firewall with Advanced Security.** Vytvořte nové příchozí pravidlo umožňující port TCP 27017.

V případě potřeby vytvořte pravidlo skupiny zabezpečení sítě, které umožní přístup k MongoDB mimo existující podsíť virtuální sítě Azure. Pravidla skupiny zabezpečení sítě můžete vytvořit pomocí [portálu Azure nebo](nsg-quickstart-portal.md) [Azure PowerShellu](nsg-quickstart-powershell.md). Stejně jako u pravidel brány Windows Firewall povolte port TCP 27017 do virtuálního síťového rozhraní virtuálního počítače MongoDB.

> [!NOTE]
> Tcp port 27017 je výchozí port používaný MongoDB. Tento port můžete změnit `--port` pomocí parametru při ručním spuštění `mongod.exe` nebo ze služby. Pokud změníte port, nezapomeňte v předchozích krocích aktualizovat pravidla brány Windows Firewall a skupiny zabezpečení sítě.


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili, jak nainstalovat a nakonfigurovat MongoDB na vašem virtuálním počítači se systémem Windows. Nyní můžete přistupovat k MongoDB na vašem virtuálním počítači se systémem Windows podle následujících témat v [dokumentaci MongoDB](https://docs.mongodb.com/manual/).

