---
title: Kurz vytváření infrastruktury pro cluster Service Fabric v AWS – Azure Service Fabric | Microsoft Docs
description: V tomto kurzu se naučíte, jak nastavit infrastrukturu AWS pro spuštění clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: 6b7d2223d33abb429ab5f59b14c80d43c70598dc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209646"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>Kurz: Vytvoření infrastruktury AWS pro hostování clusteru Service Fabric

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „libovolný OS a libovolný cloud“. V této sérii kurzů vytvoříte samostatný cluster hostovaný na AWS a nainstalujete na něj aplikaci.

Tento kurz je první částí série. V tomto článku vygenerujeme prostředky AWS potřebné pro hostování vašeho samostatného clusteru Service Fabric. V dalších článcích bude třeba nainstalovat samostatnou sadu Service Fabric, nainstalovat do clusteru ukázkovou aplikaci a nakonec cluster vyčistit.

V první části této série se naučíte:

> [!div class="checklist"]
> * Vytvoření sady instancí EC2
> * Úprava skupiny zabezpečení
> * Přihlášení k jedné z instancí
> * Příprava instance pro Service Fabric

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete mít účet AWS.  Pokud účet ještě nemáte, vytvořte si ho na stránce [AWS Console](https://aws.amazon.com/).

## <a name="create-ec2-instances"></a>Vytvoření instancí EC2

Přihlaste se ke konzole AWS > do vyhledávacího pole zadejte **EC2** > vyberte **EC2 Virtual Servers in the Cloud** (Virtuální servery EC2 v cloudu).

![Vyhledávání v konzole AWS][aws-console]

Vyberte **Launch Instance** (Spustit instanci), na další obrazovce zvolte **Select** (Vybrat) vedle Microsoft Windows Server 2016 Base.

![Výběr instance EC2][aws-ec2instance]

Vyberte **t2.medium**, pak vyberte **Next: Configure Instance Details** (Další: Konfigurace podrobností instancí), na další obrazovce změňte počet instancí na `3`, pak vyberte **Advanced Details** (Rozšířené) a rozbalte tuto část.

Pro propojení virtuálních počítačů do Service Fabric je třeba, aby virtuální počítače hostující vaši infrastrukturu měly stejné přihlašovací údaje.  Existují dva běžné způsoby, jak zajistit konzistenci přihlašovacích údajů: připojení všech virtuálních počítačů do stejné domény nebo nastavení stejného hesla správce na každém počítači.  V tomto kurzu nastavíme stejné heslo ke všem instancím EC2 pomocí uživatelského datového skriptu.  V produkčním prostředí je bezpečnější připojení hostitelů k doméně systému Windows.

Zadejte následující skript do pole pro uživatelská data v konzole:

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

Po zadání skriptu PowerShell vyberte **Review and Launch** (Zkontrolovat a spustit).

![Kontrola a spuštění skriptu na EC2][aws-ec2configure2]

Na obrazovce kontroly vyberte **Launch** (Spustit).  Pak v rozevíracím seznamu vyberte **Pokračovat bez páru klíčů** a zaškrtněte políčko označující, že znáte heslo.

![Výběr páru klíčů AWS][aws-keypair]

Nakonec vyberte **Launch Instances** (Spustit instance) a pak **View Instances** (Zobrazit instance).  Teď, když máte vytvořený základ pro váš cluster Service Fabric, musíte přidat do samotných instancí několik finálních konfigurací a připravit je tak na konfiguraci služby Service Fabric.

## <a name="modify-the-security-group"></a>Úprava skupiny zabezpečení

Service Fabric mezi hostiteli v clusteru celou řadu otevřených portů. Chcete-li tyto porty otevřít v infrastruktuře AWS, vyberte jednu z vytvořených instancí. Pak vyberte název skupiny zabezpečení, například **launch-wizard-1**. Vyberte kartu **Inbound** (Příchozí).

Nechcete-li tyto porty otevřít pro celý svět, můžete je místo otevřít jen pro hostitele ve stejné skupině zabezpečení. Poznamenejte si ID skupiny zabezpečení, v tomto příkladu je to **sg c4fb1eba**.  Pak vyberte **Edit** (Upravit).

Dále přidejte do skupiny zabezpečení čtyři pravidla pro závislosti služeb a pak tři další pro samotnou službu Service Fabric. První pravidlo umožňuje provoz protokolu ICMP pro základní kontrolu připojení. Ostatní pravidla otvírají potřebné porty, které umožní protokoly SMB a Remote Registry.

Pro první pravidlo vyberte **Add Rule** (Přidat pravidlo) a z rozevíracího seznamu vyberte **All ICMP – IPv4**. Zaškrtněte políčko vedle pole pro vlastní data a zadejte ID skupiny zabezpečení, viz výše.

Další tři závislosti nastavte obdobným způsobem.  Vyberte **Add Rule** (Přidat pravidlo), z rozevíracího seznamu vyberte možnost **Custom TCP Rule** (Vlastní pravidlo TCP) a jako rozsah portů pro jednotlivá pravidla zadejte `135`, `137-139` a `445`. Nakonec do pole zdroje zadejte ID vaší skupiny zabezpečení.

![Porty skupiny zabezpečení][aws-ec2securityports]

Teď, když jsou porty pro závislosti otevřené, musíte totéž udělat pro porty, které ke komunikaci používá samotná služba Service Fabric. Vyberte **Add Rule** (Přidat pravidlo), z rozevíracího seznamu vyberte možnost **Custom TCP Rule** (Vlastní pravidlo TCP), jako rozsah portů zadejte `20001-20031` a do pole zdroj zadejte skupinu zabezpečení.

Dál přidejte pravidlo pro rozsah dočasných portů.  Vyberte **Add Rule** (Přidat pravidlo), z rozevíracího seznamu vyberte možnost **Custom TCP Rule** (Vlastní pravidlo TCP) a jako rozsah portů zadejte `20606-20861`. Nakonec do pole zdroje zadejte ID vaší skupiny zabezpečení.

Poslední dvě pravidla by měla Service Fabric zpřístupnit světu, abyste cluster mohli spravovat ze svého počítače. Vyberte **Add Rule** (Přidat pravidlo), z rozevíracího seznamu vyberte možnost **Custom TCP Rule** (Vlastní pravidlo TCP), jako rozsah portů zadejte v prvním kroku `19000-19003` a v druhém `19080-19081` a v obou krocích vyberte v rozevíracím seznamu Source (Zdroj) možnost Anywhere (Libovolný).

Nakonec potřebujeme otevřít port 8080, abyste viděli aplikaci po nasazení. Vyberte **Add Rule** (Přidat pravidlo), z rozevíracího seznamu vyberte možnost **Custom TCP Rule** (Vlastní pravidlo TCP), jako rozsah portů zadejte `8080` a v rozevíracím seznamu Source (Zdroj) vyberte možnost Anywhere (Libovolný).

Všechna pravidla jsou nyní nastavená. Vyberte **Save** (Uložit).

## <a name="connect-to-an-instance-and-validate-connectivity"></a>Připojení k instanci a ověření připojení

Na kartě skupiny zabezpečení vyberte z nabídky nalevo **Instances** (Instance).  Vyberte postupně instance, které jste vytvořili, a poznamenejte si jejich privátní IP adresy. V následujících příkladech jsou to `172.31.21.141` a `172.31.20.163`.

Až budete mít všechny IP adresy, vyberte jednu z instancí, ke které se připojíte – klikněte na ni pravým tlačítkem myši a vyberte **Connect** (Připojit).  Tady můžete stáhnout soubor RDP pro tuto konkrétní instanci.  Vyberte **Download Remote Desktop File** (Stáhnout soubor vzdálené plochy) a pak otevřením staženého souboru vytvořte připojení ke vzdálené ploše (RDP) této instance.  Po výzvě zadejte heslo `serv1ceF@bricP@ssword`.

![Stažení souboru vzdálené plochy][aws-rdp]

Po úspěšném připojení k vaší instanci ověřte, že funguje spojení mezi instancemi a sdílení souborů.  Máte shromážděné IP adresy všech instancí, vyberte tedy jednu, ke které právě nejste připojeni. Otevřete nabídku **Start**, zadejte `cmd` a vyberte **Příkazový řádek**.

V těchto příkladech bylo navázáno spojení RDP s IP adresou 172.31.21.141. Testy připojení budou prováděny s IP adresou 172.31.20.163.

K ověření, jestli základní připojení funguje, použijeme příkaz ping.

```
ping 172.31.20.163
```

Pokud jeho výstup vypadá takto `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` a opakuje se čtyřikrát, pak spojení mezi instancemi funguje.  Nyní následujícím příkazem ověřte, jestli funguje sdílení SMB:

```
net use * \\172.31.20.163\c$
```

Výstupem by mělo být `Drive Z: is now connected to \\172.31.20.163\c$.`.

## <a name="prep-instances-for-service-fabric"></a>Příprava instancí pro Service Fabric

Pokud jste tento postup prováděli „na zelené louce“, musíte udělat ještě pár kroků navíc.  Konkrétně potřebujete ověřit, že funguje vzdálený registr, povolit protokol SMB a otevřít porty požadované pro SMB a vzdálený registr.

Pro usnadnění můžete tohle všechno provést při spouštění instancí pomocí uživatelského skriptu.

K povolení SMB použijte tento příkaz PowerShellu:

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

Pro otevření portů v bráně firewall zase slouží tento příkaz PowerShellu:

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>Další kroky

V první části této série jste se naučili, jak spustit tři instance EC2 a nakonfigurovat je pro instalaci služby Service Fabric:

> [!div class="checklist"]
> * Vytvoření sady instancí EC2
> * Úprava skupiny zabezpečení
> * Přihlášení k jedné z instancí
> * Příprava instance pro Service Fabric

Pokračujte druhou částí konfigurace služby Service Fabric v clusteru.

> [!div class="nextstepaction"]
> [Instalace služby Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png