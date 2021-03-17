---
title: Příprava na změnu příchozí IP adresy
description: Pokud se vaše příchozí IP adresa bude měnit, zjistěte, co dělat, aby vaše aplikace po provedení změny i nadále fungovala.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74672422"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Příprava na změnu příchozí IP adresy

Pokud se vám zobrazilo oznámení, že se příchozí IP adresa vaší aplikace Azure App Service mění, postupujte podle pokynů v tomto článku.

## <a name="determine-if-you-have-to-do-anything"></a>Určení, jestli je nutné provést cokoli

* Možnost 1: Pokud vaše aplikace App Service nemá vlastní doménu, nevyžaduje se žádná akce.

* Možnost 2: Pokud je v portálu pro registraci domény nakonfigurovaný jenom záznam CNAME (záznam DNS odkazující na identifikátor URI) (poskytovatel DNS nebo Azure DNS) třetí strany, nevyžaduje se žádná akce.

* Možnost 3: Pokud je záznam A (záznam DNS ukazující přímo na vaši IP adresu) nakonfigurovaný na vašem portálu pro registraci domény (poskytovatel DNS nebo Azure DNS), nahraďte stávající IP adresu novou. Novou IP adresu najdete podle pokynů v následující části.

* Možnost 4: Pokud je vaše aplikace za nástrojem pro vyrovnávání zatížení, filtrem IP nebo jakýmkoli jiným mechanismem IP adres, který vyžaduje IP adresu vaší aplikace, nahraďte stávající IP adresu novým. Novou IP adresu najdete podle pokynů v následující části.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Najít novou příchozí IP adresu v Azure Portal

Nová příchozí IP adresa, která je dána vaší aplikaci, je na portálu v poli **virtuální IP adresa** . Tuto novou IP adresu i starou je teď připojené k vaší aplikaci a později se její starý odpojí.

1.  Otevřete web [Azure Portal](https://portal.azure.com).

2.  V navigační nabídce na levé straně vyberte **App Services**.

3.  Ze seznamu vyberte svou aplikaci App Service.

1.  Pokud je aplikace Function App, přečtěte si téma [příchozí IP adresa aplikace Function App](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  V záhlaví **Nastavení** klikněte na tlačítko **vlastnosti** v levém navigačním panelu a vyhledejte oddíl označený **virtuální IP adresou**.

5. Zkopírujte IP adresu a překonfigurujte záznam domény nebo mechanismus IP adres.

## <a name="next-steps"></a>Další kroky

Tento článek vysvětluje, jak připravit na změnu IP adresy, kterou iniciovala Azure. Další informace o IP adresách v Azure App Service najdete [v tématu příchozí a odchozí IP adresy v Azure App Service](overview-inbound-outbound-ips.md).
