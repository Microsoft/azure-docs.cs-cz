---
title: Zobrazení a úpravy názvů hostitelů | Microsoft Docs
description: Jak zobrazit a změnit názvy hostitelů pro virtuální počítače Azure, webové a pracovní role pro překlad názvů
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ed250e3f32965fc450102fb14b93b93d6753ab3e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222781"
---
# <a name="viewing-and-modifying-hostnames"></a>Zobrazení a úpravy názvů hostitelů
Chcete-li, aby na instance role odkazoval název hostitele, je nutné nastavit hodnotu pro název hostitele v konfiguračním souboru služby pro každou roli. To uděláte tak, že přidáte požadovaný název hostitele do atributu **vmName** elementu **role** . Hodnota atributu **vmName** se používá jako základ pro název hostitele každé instance role. Například pokud **vmName** je *webrole* a existují tři instance této role, názvy hostitelů instancí budou *webrole0*, *webrole1* a *webrole2*. Nemusíte zadávat název hostitele pro virtuální počítače v konfiguračním souboru, protože název hostitele pro virtuální počítač je vyplněný na základě názvu virtuálního počítače. Další informace o konfiguraci služby Microsoft Azure najdete v tématu [schéma konfigurace služby Azure (soubor. cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100)) .

## <a name="viewing-hostnames"></a>Zobrazení názvů hostitelů
Názvy hostitelů virtuálních počítačů a instancí rolí v cloudové službě můžete zobrazit pomocí kteréhokoli z následujících nástrojů.

### <a name="service-configuration-file"></a>Konfigurační soubor služby
Konfigurační soubor služby pro nasazenou službu si můžete stáhnout z okna **Konfigurace** služby v Azure Portal. Pak můžete vyhledat atribut **vmName** elementu **název role** , abyste viděli název hostitele. Mějte na paměti, že tento název hostitele se používá jako základ pro název hostitele každé instance role. Například pokud **vmName** je *webrole* a existují tři instance této role, názvy hostitelů instancí budou *webrole0*, *webrole1* a *webrole2*.

### <a name="remote-desktop"></a>Vzdálená plocha
Po povolení připojení vzdálené plochy (Windows), vzdálené komunikace Windows PowerShellu (Windows) nebo SSH (Linux a Windows) k virtuálním počítačům nebo instancím rolí můžete název hostitele zobrazit v aktivním připojení ke vzdálené ploše různými způsoby:

* Do příkazového řádku nebo terminálu SSH zadejte název hostitele.
* Do příkazového řádku zadejte ipconfig/all (jenom Windows).
* Zobrazí název počítače v nastavení systému (pouze Windows).

### <a name="azure-service-management-rest-api"></a>REST API správy služeb Azure
Z klienta REST postupujte podle těchto pokynů:

1. Ujistěte se, že máte klientský certifikát pro připojení k Azure Portal. Pokud chcete získat certifikát klienta, postupujte podle kroků uvedených v tématu [Postupy: stažení a import nastavení publikování a informací o předplatném](/previous-versions/dynamicsnav-2013/dn385850(v=nav.70)). 
2. Nastavte položku záhlaví s názvem x-MS-Version s hodnotou 2013-11-01.
3. Odeslat žádost v následujícím formátu: https: \/ /Management.Core.Windows.NET/ \<subscrition-id\> /Services/hostedservices/ \<service-name\> ? embed-detail = true
4. Vyhledejte element **hostname** pro každý element **RoleInstance** .

> [!WARNING]
> Můžete také zobrazit interní příponu domény pro cloudovou službu z odpovědi na volání REST kontrolou prvku **InternalDnsSuffix** nebo spuštěním příkazu ipconfig/all z příkazového řádku v relaci vzdálené plochy (Windows) nebo spuštěním funkce Cat/etc/resolv.conf z terminálu SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Úprava názvu hostitele
Můžete upravit název hostitele pro libovolný virtuální počítač nebo instanci role tak, že nahrajete upravený konfigurační soubor služby nebo přejmenujete počítač z relace vzdálené plochy.

## <a name="next-steps"></a>Další kroky
[Překlad názvů (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schéma konfigurace služby Azure (. cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100))

[Schéma konfigurace Azure Virtual Network](/previous-versions/azure/reference/jj157100(v=azure.100))

[Zadání nastavení DNS pomocí konfiguračních souborů sítě](/previous-versions/azure/virtual-network/virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file)