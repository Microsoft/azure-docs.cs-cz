---
title: Zobrazení a úprava názvy hostitelů | Microsoft Docs
description: Jak zobrazit a změnit názvy hostitelů pro virtuální počítače Azure, webové a rolí pracovního procesu pro překlad
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: genli
ms.openlocfilehash: 6fe3522c1b3e2f5a07de3d12875ae47a830873d3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="viewing-and-modifying-hostnames"></a>Zobrazení a úprava názvy hostitelů
Pokud chcete povolit instance role bude odkazovat podle názvu hostitele, je nutné nastavit hodnotu pro název hostitele v souboru konfigurace služby pro každou roli. To uděláte tak, že přidáte název požadovaného hostitele má **vmName** atribut **Role** elementu. Hodnota **vmName** atribut slouží jako základ pro název hostitele každá instance role. Například pokud **vmName** je *webrole* a jsou tři instance dané role, bude názvy hostitelů instancí *webrole0*, *webrole1*, a *webrole2*. Není potřeba zadat název hostitele pro virtuální počítače v konfiguračním souboru, protože název hostitele pro virtuální počítač je vyplněný, na základě názvu virtuálního počítače. Další informace o konfiguraci služby Microsoft Azure najdete v tématu [schéma konfigurace služby Azure (.cscfg souboru)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Zobrazení názvů hostitelů
Názvy hostitelů virtuálních počítačů a instancí rolí můžete zobrazit v cloudové službě pomocí některé z nástrojů uvedených dole.

### <a name="azure-portal"></a>Azure Portal
Můžete použít [portál Azure](http://portal.azure.com) zobrazíte názvy hostitelů pro virtuální počítače v okně Přehled pro virtuální počítač. Mějte na paměti, která okna zobrazuje hodnotu pro **název** a **název hostitele**. I když se původně shodují, nebude změna názvu hostitele změňte název virtuálního počítače nebo role instance.

Instance rolí lze také zobrazit na portálu Azure, ale když můžete seznam instancí v cloudové službě, se nezobrazí, název hostitele. Zobrazí se název pro každou instanci, ale tento název nepředstavuje název hostitele.

### <a name="service-configuration-file"></a>Konfigurační soubor služby
Soubor konfigurace služby pro službu nasazenou z můžete stáhnout **konfigurace** okno služby v portálu Azure. Potom můžete vyhledat **vmName** atribut pro **název Role** element, který zobrazí název hostitele. Uvědomte si, že tento název hostitele slouží jako základ pro název hostitele každá instance role. Například pokud **vmName** je *webrole* a jsou tři instance dané role, bude názvy hostitelů instancí *webrole0*, *webrole1*, a *webrole2*.

### <a name="remote-desktop"></a>Vzdálená plocha
Když povolíte vzdálená plocha (Windows), vzdálené komunikace Windows Powershellu (Windows) nebo připojení SSH (Linux a Windows) pro virtuální počítače nebo instance rolí, můžete zobrazit název hostitele z aktivního připojení vzdálené plochy různými způsoby:

* Název hostitele napište na příkazový řádek nebo SSH terminálu.
* Zadáním ipconfig/všechny do příkazového řádku (jenom Windows).
* Zobrazte název počítače v nastavení systému (jenom Windows).

### <a name="azure-service-management-rest-api"></a>Rozhraní REST API pro správu služby Azure
Z klienta REST postupujte podle těchto pokynů:

1. Ujistěte se, že máte certifikát klienta pro připojení k portálu Azure. Pokud chcete získat certifikát klienta, postupujte podle kroků v [postupy: stahování a Import nastavení publikování a informace o předplatném](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Nastavte položku záhlaví s názvem x-ms-version s hodnotou 2013-11-01.
3. Poslat žádost o v následujícím formátu: https://management.core.windows.net/ \<subscrition id\>/services/hostedservices/\<název služby\>? vložení detail = true
4. Vyhledejte **HostName** element pro každou **RoleInstance** element.

> [!WARNING]
> Můžete také zobrazit přípona interní domény pro cloudové služby z odpovědi volání REST kontrolou **InternalDnsSuffix** element, nebo spuštěním ipconfig/všechny z příkazového řádku v relaci vzdálené plochy (Windows) nebo po spuštění cat /etc/resolv.conf z terminálu SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Úprava název hostitele
Název hostitele pro všechny virtuální počítače nebo role instance můžete upravit tím, že nahrajete konfigurační soubor změny služby, nebo přejmenování počítače z relace vzdálené plochy.

## <a name="next-steps"></a>Další postup
[Překlad adres (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schéma konfigurace služby Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Schéma konfigurace virtuální sítě Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Zadejte nastavení DNS pomocí konfiguračních souborů síť](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

