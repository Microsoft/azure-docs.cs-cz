---
title: Příprava na změnu příchozí adresy IP
description: Pokud se vaše příchozí IP adresa změní, přečtěte si, co dělat, aby vaše aplikace po změně fungovala dál.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672422"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Jak se připravit na změnu příchozí IP adresy

Pokud jste obdrželi oznámení, že se mění příchozí IP adresa vaší aplikace Azure App Service, postupujte podle pokynů v tomto článku.

## <a name="determine-if-you-have-to-do-anything"></a>Zjistěte, zda musíte něco udělat

* Možnost 1: Pokud vaše aplikace App Service nemá vlastní doménu, není nutná žádná akce.

* Možnost 2: Pokud je na portálu registrace domény (poskytovatel DNS třetí strany nebo Azure DNS) nakonfigurován pouze záznam CNAME (záznam DNS směřující na identifikátor URI), není vyžadována žádná akce.

* Možnost 3: Pokud je na portálu registrace domény (poskytovatel DNS třetí strany nebo Azure DNS) nakonfigurován záznam A (záznam DNS směřující přímo na vaši IP adresu), nahraďte existující IP adresu novou. Novou IP adresu najdete podle pokynů v další části.

* Možnost 4: Pokud je vaše aplikace za vyrovnáváním zatížení, filtrem IP nebo jiným mechanismem IP, který vyžaduje IP adresu vaší aplikace, nahraďte existující IP adresu novou. Novou IP adresu najdete podle pokynů v další části.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Vyhledání nové příchozí IP adresy na webu Azure Portal

Nová příchozí IP adresa, která je přidávána vaší aplikaci, je na portálu v poli **Virtuální IP adresa.** Jak tato nová IP adresa, tak ta stará jsou nyní připojeny k vaší aplikaci a později bude ta stará odpojena.

1.  Otevřete [portál Azure](https://portal.azure.com).

2.  V levé navigační nabídce vyberte **Možnost Služby aplikací**.

3.  Ze seznamu vyberte aplikaci App Service.

1.  Pokud se jedná o aplikaci s funkcí, [přečtěte](../azure-functions/ip-addresses.md#function-app-inbound-ip-address)si informace o příchozí IP adrese aplikace Function .

4.  V záhlaví **Nastavení** klikněte v levé navigaci na **Vlastnosti** a vyhledejte oddíl s názvem **Virtuální IP adresa**.

5. Zkopírujte adresu IP a překonfigurujte záznam domény nebo mechanismus IP adresy.

## <a name="next-steps"></a>Další kroky

Tento článek vysvětluje, jak se připravit na změnu IP adresy, která byla iniciována Azure. Další informace o IP adresách ve službě Azure App Service najdete v tématu [Příchozí a odchozí IP adresy ve službě Azure App Service](overview-inbound-outbound-ips.md).
