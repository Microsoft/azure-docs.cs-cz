---
title: IT Service Management Connector – zabezpečený export v Azure Monitor
description: V tomto článku se dozvíte, jak připojit své ITSM produkty/služby k zabezpečenému exportu v Azure Monitor centrálně monitorovat a spravovat pracovní položky ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 1cd8041f801a418f67d26461c5f4e9ebff7e5c30
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507298"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Připojení Azure k nástrojům ITSM pomocí zabezpečeného exportu

V tomto článku se dozvíte, jak nakonfigurovat připojení mezi produktem nebo službou IT Service Management (ITSM) pomocí zabezpečeného exportu.

Zabezpečený export je aktualizovaná verze [konektoru IT Service Management Connector (ITSMC)](./itsmc-overview.md). Obě verze umožňují vytvářet pracovní položky v nástroji ITSM, když Azure Monitor odesílá výstrahy. Mezi tyto funkce patří upozornění na metriky, protokoly a protokoly aktivit.

ITSMC používá přihlašovací údaje uživatelského jména a hesla. Zabezpečený export má silnější ověřování, protože používá Azure Active Directory (Azure AD). Azure AD je cloudová služba Microsoftu, která slouží ke správě identit a přístupu. Pomůže uživatelům přihlašovat se k interním nebo externím prostředkům a přistupovat k nim. Použití Azure AD s ITSM pomáhá identifikovat výstrahy Azure (prostřednictvím ID aplikace Azure AD), které se poslaly do externího systému.

> [!NOTE]
> Možnost připojit Azure k nástrojům ITSM pomocí zabezpečeného exportu je ve verzi Preview.

## <a name="secure-export-architecture"></a>Architektura zabezpečeného exportu

Architektura zabezpečeného exportu zavádí následující nové funkce:

* **Nová skupina akcí**: výstrahy se odesílají do nástroje ITSM prostřednictvím skupiny akcí zabezpečeného Webhooku místo skupiny akcí ITSM, kterou ITSMC používá.
* **Ověřování Azure AD**: ověřování probíhá přes Azure AD místo přihlašovacích údajů uživatelského jména a hesla.

## <a name="secure-export-data-flow"></a>Tok dat zabezpečeného exportu

Postup pro tok dat zabezpečeného exportu je následující:

1. Azure Monitor odešle výstrahu, která je nakonfigurována pro použití zabezpečeného exportu.
2. Datová část výstrahy je odeslána zabezpečenou akcí Webhooku nástroji ITSM.
3. Aplikace ITSM zkontroluje ve službě Azure AD, pokud je výstraha autorizována k zadání nástroje ITSM.
4. Pokud je výstraha autorizována, aplikace:
   
   1. Vytvoří pracovní položku (například incident) v nástroji ITSM.
   2. Váže ID položky konfigurace (CI) k databázi správy zákazníka (CMDB).

![Diagram, který ukazuje, jak nástroj ITSM komunikuje s Azure a D, upozorněními Azure a skupinou akcí.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>Výhody zabezpečeného exportu

Hlavní výhodou integrace jsou tyto:

* **Lepší ověřování**: Azure AD poskytuje bezpečnější ověřování bez časových limitů, které se běžně vyskytují v ITSMC.
* **Upozornění vyřešená v nástroji ITSM**: výstrahy metrik implementují stavy "Trigger" a "Vyřešeno". Při splnění podmínky je stav výstrahy "aktivováno". Pokud podmínka není splněna, stav výstrahy bude "Vyřešeno". V ITSMC se výstrahy nedají automaticky vyřešit. V případě zabezpečeného exportu převedený stav vyřešen do nástroje ITSM, a proto je automaticky aktualizován.
* **[Běžné schéma výstrah](./alerts-common-schema.md)**: v ITSMC se schéma datové části výstrahy liší v závislosti na typu výstrahy. V zabezpečeném exportu existuje společné schéma pro všechny typy výstrah. Toto společné schéma obsahuje CI pro všechny typy výstrah. Všechny typy výstrah budou moci navazovat svou CI pomocí CMDB.

Začněte používat nástroj konektoru ITSM s těmito kroky:

1. Zaregistrovat aplikaci v Azure AD
2. Vytvořte zabezpečenou skupinu akcí Webhooku.
3. Nakonfigurujte své partnerské prostředí. 

Zabezpečený export podporuje připojení pomocí následujících ITSM nástrojů:
* [ServiceNow](#connect-servicenow-to-azure-monitor)
* [Helix BMC](#connect-bmc-helix-to-azure-monitor)

## <a name="register-with-azure-active-directory"></a>Zaregistrovat s Azure Active Directory

Pomocí těchto kroků zaregistrujete aplikaci do služby Azure AD:

1. Postupujte podle kroků v části [Registrace aplikace s platformou Microsoft Identity](../../active-directory/develop/quickstart-register-app.md).
2. V Azure AD vyberte **zveřejnit aplikaci**.
3. Vyberte **sadu** pro **identifikátor URI ID aplikace**.

   [![Snímek obrazovky s možností nastavení U R I aplikace I D](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. Vyberte **Uložit**.

## <a name="create-a-secure-webhook-action-group"></a>Vytvoří zabezpečenou skupinu akcí Webhooku.

Po registraci vaší aplikace ve službě Azure AD můžete vytvářet pracovní položky v nástroji ITSM založené na výstrahách Azure pomocí akce zabezpečený Webhook ve skupinách akcí.

Skupiny akcí poskytují modulární a opakovaně použitelný způsob aktivace akcí pro výstrahy Azure. V Azure Portal můžete použít skupiny akcí s upozorněními na metriky, výstrahy protokolu aktivit a výstrahy Azure Log Analytics.
Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups in the Azure Portal](./action-groups.md).

Pokud chcete přidat Webhook k akci, postupujte podle těchto pokynů pro zabezpečený Webhook:

1. V [Azure Portal](https://portal.azure.com/)vyhledejte a vyberte **monitor**. Podokno **monitorování** slučuje všechna nastavení monitorování a data v jednom zobrazení.
2. Vyberte **výstrahy**  >  **Spravovat akce**.
3. Vyberte [Přidat skupinu akcí](./action-groups.md#create-an-action-group-by-using-the-azure-portal)a vyplňte pole.
4. Do pole **název skupiny akcí** zadejte název a zadejte název do pole **krátký název** . Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.
5. Vyberte **zabezpečený Webhook**.
6. Vyberte tyto podrobnosti:
   1. Vyberte ID objektu Azure Active Directory instance, kterou jste zaregistrovali.
   2. V případě identifikátoru URI vložte adresu URL Webhooku, kterou jste zkopírovali z [prostředí nástroje ITSM](#configure-the-itsm-tool-environment).
   3. Nastavte **možnost Povolit společné schéma výstrah** na **Ano**. 

   Následující obrázek ukazuje konfiguraci ukázkové zabezpečené akce Webhooku:

   ![Snímek obrazovky, který zobrazuje zabezpečenou akci Webhooku.](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>Konfigurace prostředí nástroje ITSM

Konfigurace obsahuje 2 kroky:
1. Získejte identifikátor URI pro definici zabezpečeného exportu.
2. Definice podle toku nástroje ITSM


### <a name="connect-servicenow-to-azure-monitor"></a>Připojení ServiceNow k Azure Monitor

Následující části obsahují podrobné informace o tom, jak připojit produkt ServiceNow a zabezpečený export v Azure.

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste splnili následující požadavky:

* Služba Azure AD je zaregistrovaná.
* Máte podporovanou verzi ServiceNow Event Management-ITOM (verze Orlandu nebo novější).

### <a name="configure-the-servicenow-connection"></a>Konfigurace připojení ServiceNow

1. použijte linku https://(název instance). Service-Now. com/API/sn_em_connector/em/inbound_event? source = azuremonitor identifikátor URI pro definici zabezpečeného exportu.

2. Postupujte podle pokynů podle verze:
   * [Paříž](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlandu](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)

### <a name="connect-bmc-helix-to-azure-monitor"></a>Připojení řadiče pro správu základní desky Helix k Azure Monitor

V následujících částech najdete podrobné informace o tom, jak připojit Helix produkt pro řadiče pro správu základní desky a zabezpečený export v Azure.

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste splnili následující požadavky:

* Služba Azure AD je zaregistrovaná.
* Máte podporovanou verzi nástroje BMC Helix multi-Cloud Service Management (verze 19,08 nebo novější).

### <a name="configure-the-bmc-helix-connection"></a>Konfigurace připojení Helix řadiče pro správu základní desky

1. Pokud chcete získat identifikátor URI pro zabezpečený export, použijte následující postup v prostředí Helix BMC:

   1. Přihlaste se k integračnímu studiu.
   2. Vyhledejte **vytvořit incident z toku upozornění Azure** .
   3. Zkopírujte adresu URL Webhooku.
   
   ![Snímek Webhooku U R L v Integration studiu](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)
   
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

![Snímek obrazovky, který zobrazuje konfiguraci řadiče pro správu základní desky.](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)




## <a name="next-steps"></a>Další kroky

* [Vytváření pracovních položek ITSM z výstrah Azure](./itsmc-overview.md)
