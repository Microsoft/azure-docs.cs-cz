---
title: IT Service Management Connector – zabezpečený export v Azure Monitor
description: Tento článek poskytuje informace o tom, jak připojit produkty nebo služby ITSM k zabezpečenému exportu v Azure Monitor k centrálnímu monitorování a správě pracovních položek ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 160054e7e98dc2cb06c2c7daf325536766963daa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568638"
---
# <a name="connect-azure-to-itsm-tools-using-secure-export"></a>Připojení Azure k nástrojům ITSM pomocí zabezpečeného exportu

Tento článek poskytuje informace o tom, jak nakonfigurovat připojení mezi produktem ITSM nebo službou pomocí zabezpečeného exportu.

Zabezpečený export je a aktualizovaná verze [ITSMC](./itsmc-overview.md) (Správa služeb IT). Obě verze umožňují vytvářet pracovní položky v nástroji ITSM, když se aktivují výstrahy Azure Monitor. Mezi tyto funkce patří upozornění na metriky, protokoly a protokoly aktivit.

[ITSMC](./itsmc-overview.md) používá přihlašovací údaje uživatele a hesla, ale zabezpečený export má silnější ověřování, protože používá Azure Active Directory (Azure AD). Azure Active Directory (Azure AD) je cloudová služba pro správu identit a přístupu od Microsoftu. Pomůže uživatelům přihlašovat se k interním nebo externím prostředkům a přistupovat k nim. Použití Azure AD s ITSM pomáhá identifikovat výstrahy Azure (pomocí ID aplikace Azure AD), které se poslaly do externího systému.

> [!NOTE]
> Připojení k nástrojům Azure to ITSM pomocí zabezpečeného exportu je ve verzi Preview.

## <a name="secure-export-architecture"></a>Architektura zabezpečeného exportu

Architektura zabezpečeného exportu zavádí následující nové funkce:

* **Nová skupina akcí** – výstrahy se odesílají do nástroje ITSM pomocí skupiny akcí zabezpečeného Webhooku (místo ITSM skupiny akcí pomocí v ITSMC).
* **Ověřování Azure AD** – ověřování probíhá pomocí Azure AD místo přihlašovacích údajů uživatele a hesla.

## <a name="secure-export-data-flow"></a>Tok dat zabezpečeného exportu

Postup pro tok dat zabezpečeného exportu:

1) Výstraha, která je nakonfigurována pro použití zabezpečeného exportu, je aktivována v Azure Monitor
2) Datová část výstrahy je odeslána pomocí zabezpečené akce Webhooku nástroji ITSM.
3) Aplikace ITSM zkontroluje ve službě Azure AD, pokud je výstraha autorizována k zadání nástroje ITSM.
4) Pokud je výstraha autorizována aplikací:
    1) Vytvoří pracovní položku (např. incident) v nástroji ITSM.
    2) Váže ID položky konfigurace (CI) k databázi správy zákazníka (CMDB).
![Diagram ITSM](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>Připojení pomocí řadiče pro správu základní desky Helix

Zabezpečený export podporuje Helix BMC. Mezi výhody integrace patří:

* **Lepší ověřování** – Azure AD poskytuje bezpečnější ověřování bez časových limitů, které se běžně vyskytují v ITSMC.
* **Výstrahy vyřešené v nástroji ITSM** – výstrahy metriky implementují stav "aktivováno" a "Vyřešeno". Když je podmínka splněna, stav výstrahy je "aktivováno". Pokud podmínka není splněna, stav výstrahy je "Vyřešeno". V ITSMC se výstrahy nedaly automaticky vyřešit. V případě zabezpečeného exportu převedený stav vyřešen do nástroje ITSM, takže se automaticky aktualizuje.
* **[Společné schéma povoluje](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)** – v ITSMC se schéma datové části výstrahy liší v závislosti na typu výstrahy. V zabezpečeném exportu máme společné schéma pro všechny typy výstrah. Toto nové společné schéma obsahuje CI pro všechny typy výstrah. Tím, že všechny typy výstrah budou moci vytvořit vazby ke své CI pomocí CMDB.

Začněte používat konektor ITSM s těmito kroky:

1. Zaregistrujte svoji aplikaci pomocí Azure Active Directory.
2. Vytvořte zabezpečenou skupinu akcí Webhooku.
3. Nakonfigurujte své partnerské prostředí.

## <a name="register-with-azure-active-directory"></a>Zaregistrovat s Azure Active Directory

Pomocí těchto kroků zaregistrujete aplikaci Azure AD s Azure Active Directory

1) [Vytváření Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
2) V Azure Active Directory vyberte "vystavení aplikace".
3) Vyberte nastavit pro identifikátor URI ID aplikace [ ![ Azure AD](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox) .
4) Klikněte na Uložit.

## <a name="create-a-secure-webhook-action-group"></a>Vytvoří zabezpečenou skupinu akcí Webhooku.

Jakmile máte službu Azure AD zaregistrovanou, můžete v nástroji ITSM vytvořit pracovní položky založené na výstrahách Azure pomocí akce zabezpečený Webhook ve skupinách akcí.
Skupiny akcí poskytují modulární a opakovaně použitelný způsob aktivace akcí pro vaše výstrahy Azure. Skupiny akcí s upozorněními na metriky, upozornění protokolu aktivit a výstrahy služby Azure Log Analytics můžete používat v Azure Portal.
Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups in the Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
Použijte následující postup:

V prostředí Helix BMC:

1. Přihlaste se k integračnímu studiu.
2. Vyhledejte vytvořit incident z toku upozornění Azure.
3. Zkopírujte adresu URL Webhooku.
![ADRESA URL ŘADIČE PRO SPRÁVU ZÁKLADNÍ DESKY](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

Pokud chcete přidat Webhook k akci, postupujte podle pokynů pro zabezpečený Webhook:

1. V [Azure Portal](https://portal.azure.com/)vyhledejte a vyberte **monitor**. Podokno **monitorování** slučuje všechna nastavení monitorování a data v jednom zobrazení.
2. Vyberte **Upozornění** a pak vyberte **Spravovat akce**.
3. Vyberte [Přidat skupinu akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal)a vyplňte pole.
4. Do pole **název skupiny akcí** zadejte název a zadejte název do pole **krátký název** . Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.
5. Vybrat **zabezpečený Webhook**
6. Vyberte Upravit podrobnosti. Následující obrázek ukazuje ukázku zabezpečené akce Webhooku:
    1. Vyberte správné ID objektu Azure Active Directory, který jste zaregistrovali.
    2. Do pole v identifikátoru URI vložte adresu URL Webhooku, kterou jste zkopírovali z "prostředí BMC Helix".
    3. Nastavte **běžné schéma výstrah** na **Ano**. 
7. Následující obrázek ukazuje ukázku zabezpečené konfigurace akce Webhooku: ![ zabezpečený Webhook](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-partner-environment"></a>Konfigurace partnerského prostředí

### <a name="connect-bmc-helix-to-azure-monitor"></a>Připojení řadiče pro správu základní desky Helix k Azure Monitor

V následující části najdete podrobné informace o tom, jak připojit Helix produkt pro řadiče pro správu základní desky a zabezpečený export v Azure.

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jsou splněné následující předpoklady:

* Služba Azure AD je zaregistrovaná.
* Podporujete podporovanou verzi nástroje BMC Helix multi-Cloud Service Management: 20,02 verze nebo novější.

Konfigurace připojení Helix řadiče pro správu základní desky:

1) [Povolení předem připravené integrace s Azure Monitor pro verzi 20,2](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)

2) Jako součást konfigurace připojení v Helix BMC přejděte do instance Integration BMC a postupujte podle pokynů:

1. Vybrat **katalog**
2. Vybrat **výstrahy Azure**
3. Vybrat **konektory**
4. Vybrat **konfiguraci**
5. Vyberte **Přidat novou konfiguraci připojení** .
6. Vyplňte informace pro konfigurační oddíl.
    1. **Název** – Udělejte si vlastní
    2. **Typ autorizace** – žádný
    3. **Popis**– Udělejte si vlastní
    4. **Lokalita**– Cloud
    5. **Počet instancí** – 2 – výchozí hodnota
    6. **Zaškrtněte políčko** ve výchozím nastavení vybrané a povolte použití.
    7. ID tenanta Azure, ID aplikace Azure se převezme z aplikace, která byla definovaná v kroku "vytvořená Azure Active Directory".
![Konfigurace řadiče pro správu základní desky](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>Další kroky

* [Vytváření pracovních položek ITSM z výstrah Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
