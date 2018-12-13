---
title: Příprava na změnu adresy SSL IP – Azure App Service
description: Pokud je vaše adresa SSL IP změnit, zjistěte, co můžete udělat tak, aby vaše aplikace i nadále fungovat po provedení změny.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4de20ba46d3c16d6cd7bde8e17efb0a6212bc771
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270786"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Postup přípravy na změnu adresy SSL IP

Pokud jste dostali oznámení, že se mění SSL IP adresu vaší aplikace Azure App Service, postupujte podle pokynů v tomto článku na verze existující adresu SSL IP a přiřadit nový.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Uvolnit adresy SSL IP a přiřaďte nové značky

1.  Otevřete web [Azure Portal](https://portal.azure.com).

2.  V levé navigační nabídce vyberte **App Services**.

3.  Vyberte ze seznamu vaši aplikaci služby App Service.

4.  V části **nastavení** záhlaví, klikněte na tlačítko **nastavení SSL** v levém navigačním panelu.

1. V části vazby SSL vyberte záznam název hostitele. V editoru, které se otevře, zvolte **SNI SSL** na **typ SSL** rozevírací nabídky a klikněte na tlačítko **přidat vazbu**. Když se zobrazí zpráva o úspěchu operací, stávající IP adresa byla uvolněna.

6.  V **vazby SSL** znovu vyberte záznam stejného názvu hostitele s certifikátem. V editoru, které se otevře, zvolte tuto chvíli **IP SSL na základě** na **typ SSL** rozevírací nabídky a klikněte na tlačítko **přidat vazbu**. Když se zobrazí zpráva o úspěchu operací, které jste získali novou IP adresu.

7.  Pokud je záznam (záznam DNS odkazuje přímo na IP adresu) nakonfigurované v portálu pro registraci vaší domény (třetích stran poskytovatele DNS nebo Azure DNS), nahraďte existující IP adresu nově vygenerovaný. Podle pokynů uvedených v následující části najdete novou IP adresu.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Na webu Azure Portal najdete novou adresu SSL IP

1.  Počkejte pár minut a pak otevřete [webu Azure portal](https://portal.azure.com).

2.  V levé navigační nabídce vyberte **App Services**.

3.  Vyberte ze seznamu vaši aplikaci služby App Service.

4.  V části **nastavení** záhlaví, klikněte na tlačítko **vlastnosti** v levém navigačním panelu a najít v části s názvem **virtuální IP adresa**.

5. Zkopírujte IP adresu a překonfigurujte záznamu domény nebo IP mechanismus.

## <a name="next-steps"></a>Další postup

Tento článek vysvětlil, jak připravit pro změnu IP adresy, který byl inicializován nástrojem Azure. Další informace o IP adresách v Azure App Service najdete v tématu [SSL a SSL IP adres ve službě Azure App Service](app-service-ip-addresses.md).
