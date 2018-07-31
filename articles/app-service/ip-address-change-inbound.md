---
title: Jak připravit pro příchozí změnu IP adresy – Azure
description: Pokud chcete změnit vaše příchozí IP adresa, zjistěte, co můžete udělat tak, aby vaše aplikace i nadále fungovat po provedení změny.
services: app-service\web
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: tdykstra
ms.openlocfilehash: 5d30357e5308de0224590703bb5e68fbe73b882b
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343431"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Postup přípravy na změnu příchozí IP adresy

Pokud jste dostali oznámení, že se mění příchozí IP adresu vaší aplikace Azure App Service, postupujte podle pokynů v tomto článku.

## <a name="determine-if-you-have-to-do-anything"></a>Zjistit, pokud máte dělat nic.

* Možnost 1: Pokud vaše aplikace app Service nemá žádné vlastní domény, nemusíte nic dělat.

* Možnost 2: Pokud pouze záznam CNAME (záznam DNS odkazuje na identifikátor URI) je nakonfigurován v portálu pro registraci vaší domény (třetích stran poskytovatele DNS nebo Azure DNS), nemusíte nic dělat.

* Možnost 3: Pokud je záznam (záznam DNS odkazuje přímo na IP adresu) nakonfigurované v portálu pro registraci vaší domény (třetích stran poskytovatele DNS nebo Azure DNS), nahraďte existující IP adresu s novým. Podle pokynů uvedených v následující části najdete novou IP adresu.

* Možnost 4: Pokud je vaše aplikace za nástroj pro vyrovnávání zatížení, filtr nebo jiný mechanismus IP, která vyžaduje vaše aplikace IP adresu, nahraďte existující IP adresu s novým. Podle pokynů uvedených v následující části najdete novou IP adresu.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Zjistit novou příchozí IP adresu na webu Azure Portal

Novou příchozí IP adresu, dostane do vaší aplikace je v portálu **virtuální IP adresa** pole. Tuto novou IP adresu a ten starý jsou připojené k vaší aplikaci nyní a později se starou odpojí.

1.  Otevřete web [Azure Portal](https://portal.azure.com).

2.  V levé navigační nabídce vyberte **App Services**.

3.  Vyberte ze seznamu vaši aplikaci služby App Service.

4.  Pokud je aplikace function app, naleznete v tématu [aplikace Function app příchozí IP adresu](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  V části **nastavení** záhlaví, klikněte na tlačítko **vlastnosti** v levém navigačním panelu a najít v části s názvem **virtuální IP adresa**.

5. Zkopírujte IP adresu a překonfigurujte záznamu domény nebo IP mechanismus.

## <a name="next-steps"></a>Další postup

Tento článek vysvětlil, jak připravit pro změnu IP adresy, který byl inicializován nástrojem Azure. Další informace o IP adresách v Azure App Service najdete v tématu [příchozí a odchozí IP adresy ve službě Azure App Service](app-service-ip-addresses.md).
