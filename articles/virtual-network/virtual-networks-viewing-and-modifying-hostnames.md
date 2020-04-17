---
title: Zobrazení a úprava názvů hostitelů | Dokumenty společnosti Microsoft
description: Jak zobrazit a změnit názvy hostitelů pro virtuální počítače Azure, webové a pracovní role pro překlad názvů
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 09c911838d34a083de8450145ce849fc966eb0e8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459147"
---
# <a name="viewing-and-modifying-hostnames"></a>Zobrazení a úprava názvů hostitelů
Chcete-li povolit, aby instance rolí odkazovaly podle názvu hostitele, je nutné nastavit hodnotu názvu hostitele v konfiguračním souboru služby pro každou roli. To uděláte přidáním požadovaného názvu hostitele do atributu **vmName** prvku **Role.** Hodnota atributu **vmName** se používá jako základ pro název hostitele každé instance role. Například pokud **vmName** je *webrole* a existují tři instance této role, názvy hostitelů instance bude *webrole0*, *webrole1*a *webrole2*. V konfiguračním souboru není nutné zadávat název hostitele pro virtuální počítače, protože název hostitele virtuálního počítače je naplněn na základě názvu virtuálního počítače. Další informace o konfiguraci služby Microsoft Azure najdete v tématu [Schéma konfigurace služby Azure (soubor cscfg).](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Zobrazení názvů hostitelů
Názvy hostitelů virtuálních počítačů a instancí rolí v cloudové službě můžete zobrazit pomocí některého z níže uvedených nástrojů.

### <a name="service-configuration-file"></a>Konfigurační soubor služby
Konfigurační soubor služby pro nasazenou službu si můžete stáhnout z okna **Konfigurovat** službu na webu Azure Portal. Potom můžete vyhledat atribut **vmName** pro element **název role** a zobrazit název hostitele. Mějte na paměti, že tento název hostitele se používá jako základ pro název hostitele každé instance role. Například pokud **vmName** je *webrole* a existují tři instance této role, názvy hostitelů instance bude *webrole0*, *webrole1*a *webrole2*.

### <a name="remote-desktop"></a>Vzdálená plocha
Po povolení připojení vzdálené plochy (Windows), prostředí Windows PowerShell vzdálené komunikace (Windows) nebo SSH (Linux a Windows) k virtuálním počítačům nebo instancí rolí můžete zobrazit název hostitele z aktivního připojení ke vzdálené ploše různými způsoby:

* Na příkazovém řádku nebo terminálu SSH zadejte název hostitele.
* Na příkazovém řádku zadejte ipconfig /all (pouze systém Windows).
* Zobrazte název počítače v nastavení systému (pouze systém Windows).

### <a name="azure-service-management-rest-api"></a>Rozhraní REST správy služeb Azure
Z klienta REST postupujte podle následujících pokynů:

1. Ujistěte se, že máte klientský certifikát pro připojení k portálu Azure. Chcete-li získat klientský certifikát, postupujte podle pokynů uvedených v [části Postup: Stažení a import nastavení publikování a Informace o předplatném](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Nastavte položku záhlaví s názvem x-ms-version s hodnotou 2013-11-01.
3. Odeslat požadavek v následujícím\/formátu:\<https: /management.core.windows.net/\>subscrition-id\</services/hostedservices/ service-name\>?embed-detail=true
4. Vyhledejte prvek **HostName** pro každý element **Instance RoleInstance.**

> [!WARNING]
> Můžete také zobrazit příponu interní domény pro cloudovou službu z odpovědi volání REST kontrolou prvku **InternalDnsSuffix** nebo spuštěním ipconfig /all z příkazového řádku v relaci vzdálené plochy (Windows) nebo spuštěním cat /etc/resolv.conf z terminálu SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Úprava názvu hostitele
Název hostitele pro libovolný virtuální počítač nebo instanci role můžete upravit nahráním upraveného konfiguračního souboru služby nebo přejmenováním počítače z relace vzdálené plochy.

## <a name="next-steps"></a>Další kroky
[Překlad názvů (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schéma konfigurace služby Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Schéma konfigurace virtuální sítě Azure](https://go.microsoft.com/fwlink/?LinkId=248093)

[Určení nastavení DNS pomocí konfiguračních souborů sítě](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

