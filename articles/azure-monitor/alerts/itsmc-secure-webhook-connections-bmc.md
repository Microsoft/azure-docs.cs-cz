---
title: IT Service Management Connector – zabezpečení exportu v Azure Monitor – konfigurace s řadičem pro správu základní desky
description: V tomto článku se dozvíte, jak propojit své ITSM produkty/služby s řadičem pro správu základní desky při zabezpečeném exportu v Azure Monitor.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7c50d533f95e1131e26ddd808c77023ae4591a26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041701"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>Připojení řadiče pro správu základní desky Helix k Azure Monitor

V následujících částech najdete podrobné informace o tom, jak připojit Helix produkt pro řadiče pro správu základní desky a zabezpečený export v Azure.

## <a name="prerequisites"></a>Předpoklady

Ujistěte se, že jste splnili následující požadavky:

* Služba Azure AD je zaregistrovaná.
* Máte podporovanou verzi nástroje BMC Helix multi-Cloud Service Management (verze 19,08 nebo novější).

## <a name="configure-the-bmc-helix-connection"></a>Konfigurace připojení Helix řadiče pro správu základní desky

1. K získání identifikátoru URI pro zabezpečený export použijte následující postup v prostředí Helix BMC:

   1. Přihlaste se k integračnímu studiu.
   1. Vyhledejte **vytvořit incident z toku upozornění Azure** .
   1. Zkopírujte adresu URL Webhooku.
   
   ![Snímek Webhooku U R L v Integration studiu](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. Postupujte podle pokynů podle verze:
   * [Povoluje se předem vytvořená integrace s Azure monitor pro verzi 20,02](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [Povoluje se předem vytvořená integrace s Azure monitor pro verzi 19,11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

3. Jako součást konfigurace připojení v BMC Helix přejděte do instance Integration BMC a postupujte podle těchto pokynů:

   1. Vyberte **katalog**.
   2. Vyberte **výstrahy Azure**.
   3. Vyberte **konektory**.
   4. Vyberte **Konfigurace**.
   5. Vyberte **Přidat konfiguraci nového připojení** .
   6. Vyplňte informace pro konfigurační oddíl:
      - **Název**: Udělejte si vlastní.
      - **Typ autorizace**: **žádné**
      - **Popis**: Udělejte si vlastní.
      - **Lokalita**: **Cloud**
      - **Počet instancí**: **2**, výchozí hodnota.
      - Pokud chcete povolit použití, **zaškrtněte políčko**: výchozí.
      - ID tenanta Azure a ID aplikace Azure jsou pořízené z aplikace, kterou jste definovali dříve.

![Snímek obrazovky, který zobrazuje konfiguraci řadiče pro správu základní desky.](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)
