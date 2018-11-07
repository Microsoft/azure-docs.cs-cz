---
title: Zobrazení a úpravy názvů hostitelů | Dokumentace Microsoftu
description: Jak zobrazit a změnit názvy hostitelů pro virtuální počítače Azure, web a role pracovního procesu pro překlad názvů
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: afd5361774af3379dc0d4054509e318e8263dcc5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250182"
---
# <a name="viewing-and-modifying-hostnames"></a>Zobrazení a úpravy názvů hostitelů
Pokud chcete povolit vaše instance rolí se nesmí odkazovat podle názvu hostitele, musíte nastavit hodnotu pro název hostitele v konfiguračním souboru služby pro každou roli. Můžete to udělat tak, že přidáte název požadovaného hostitele do **vmName** atribut **Role** elementu. Hodnota **vmName** atribut se používá jako základ pro název hostitele instance každé role. Například pokud **vmName** je *webrole* a existují tři instance dané role, budou se názvy hostitelů instancí *webrole0*, *webrole1*, a *webrole2*. Není potřeba zadat název hostitele pro virtuální počítače v konfiguračním souboru, protože název hostitele pro virtuální počítač se vyplní na základě názvu virtuálního počítače. Další informace o konfiguraci služby Microsoft Azure najdete v tématu [schématu konfigurace služby Azure (.cscfg souboru)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Zobrazení názvů hostitelů
Názvy hostitelů virtuálních počítačů a instancí rolí v cloudové službě můžete zobrazit pomocí některé z nástrojů uvedených dole.

### <a name="service-configuration-file"></a>Konfigurační soubor služby
Můžete stáhnout konfigurační soubor služby pro službu nasazenou z **konfigurovat** okně služby na webu Azure Portal. Potom můžete hledat **vmName** atribut pro **název Role** element zobrazíte název hostitele. Uvědomte si, že tento název hostitele se používá jako základ pro název hostitele instance každé role. Například pokud **vmName** je *webrole* a existují tři instance dané role, budou se názvy hostitelů instancí *webrole0*, *webrole1*, a *webrole2*.

### <a name="remote-desktop"></a>Vzdálená plocha
Po povolení vzdálené plochy (Windows), vzdálené komunikace Windows Powershellu (Windows) nebo připojení SSH (Linux a Windows) pro virtuální počítače nebo instance rolí, zobrazí se název hostitele z aktivní připojení ke vzdálené ploše v různé způsoby:

* Zadejte název hostitele na příkazovém řádku nebo na terminál SSH.
* Zadejte ipconfig/vše do příkazového řádku (jenom Windows).
* Zobrazte název počítače v systémových nastaveních (jenom Windows).

### <a name="azure-service-management-rest-api"></a>Rozhraní REST API pro správu služeb Azure
Z klienta REST postupujte podle těchto pokynů:

1. Ujistěte se, že máte certifikát klienta pro připojení k webu Azure portal. Získejte klientský certifikát, postupujte podle kroků v [postupy: stažení a Import nastavení publikování a informace o předplatném](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Nastavte položku záhlaví x-ms-version s hodnotou 2013-11-01 s názvem.
3. Odeslat požadavek v následujícím formátu: https://management.core.windows.net/\<subscrition-id\>/services/hostedservices/\<service-name\>?embed-detail=true
4. Hledat **HostName** – element pro každé **instance role** elementu.

> [!WARNING]
> Můžete také zobrazit příponu interní domény pro cloudovou službu z odpovědi volání REST kontrolou **InternalDnsSuffix** element, nebo spuštěním příkazu ipconfig/vše z příkazového řádku v relaci vzdálené plochy (Windows) nebo po spuštění cat /etc/resolv.conf z terminálu SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Úprava názvu hostitele
Název hostitele pro virtuální počítač nebo instanci role můžete upravit tak, že nahrajete soubor konfigurace upravené služby nebo při přejmenování počítače z relace vzdálené plochy.

## <a name="next-steps"></a>Další postup
[Překlad názvů (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schéma konfigurace služby Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Schéma konfigurace Azure Virtual Network](https://go.microsoft.com/fwlink/?LinkId=248093)

[Zadání nastavení DNS pomocí konfiguračních souborech sítě](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

