---
title: Firewall webových aplikací Azure a Azure Policy
description: Firewall webových aplikací Azure (WAF) v kombinaci s Azure Policy může pomáhat vyhovět podnikovým standardům a vyhodnocovat dodržování předpisů pro prostředky WAF v rozsahu.
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: rimansdo
ms.openlocfilehash: 4c1fd53eb6ebf1f1aebdfba99b736e26bd6cff2b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85306880"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Firewall webových aplikací Azure a Azure Policy

Firewall webových aplikací Azure (WAF) v kombinaci s Azure Policy může pomáhat vyhovět standardům organizace a vyhodnocovat dodržování předpisů pro prostředky WAF v rozsahu. Azure Policy je nástroj zásad správného řízení, který poskytuje agregované zobrazení pro vyhodnocení celkového stavu prostředí s možností přechodu k podrobnostem na jednotlivé prostředky a členitosti podle zásad. Zásady Azure také pomáhají přenést prostředky do dodržování předpisů prostřednictvím hromadné nápravy pro stávající prostředky a automatickou nápravu nových prostředků.

## <a name="azure-policies-for-web-application-firewall"></a>Zásady Azure pro Firewall webových aplikací

K dispozici je několik integrovaných zásad Azure pro správu prostředků WAF. Členění zásad a jejich funkce jsou následující:

1. **Brána Firewall webových aplikací by měla být povolená pro službu Azure front-dvířks nebo Application Gateway**: služby Azure front-WAF a aplikační brány se vyhodnocují v případě, že k vytvoření prostředku existuje. Zásady mají tři důsledky: audit, DENY a Disable. Audit sleduje, když služba nebo Application Gateway služby front-end v Azure nemá WAF a umožňuje uživatelům zjistit, co služba Azure front dveří nebo Application Gateway v současnosti nedodržuje. Při odepření se zabrání tomu, aby se vytvořila služba Azure front-dveří nebo Application Gateway, pokud není WAF připojené. Disabled vypne tuto zásadu.

2. **Firewall webových aplikací by měl být režim nastavení pro Application Gateway a službu Azure front-dveří**: Brána Firewall webových aplikací se vyhodnocuje v tom, v jakém režimu je, buď prevence, nebo detekce. Zásada zajišťuje konzistenci režimu napříč firewally webových aplikací. Zásady mají tři důsledky: audit, DENY a Disable. Audit sleduje, když se WAF nevejde do zadaného režimu. Deny zabrání v vytváření jakýchkoli WAF, pokud není ve správném režimu. Disabled vypne tuto zásadu.


## <a name="launch-an-azure-policy"></a>Spustit Azure Policy


1.  Na domovské stránce Azure zadejte do panelu hledání zásady a klikněte na ikonu Azure Policy

2.  Ve službě Azure Policy v části **vytváření**vyberte **přiřazení**.

![Firewall webových aplikací Azure](../media/waf-azure-policy/policy-home.png)

3.  Na stránce přiřazení vyberte ikonu **přiřadit zásadu** v horní části.

![Firewall webových aplikací Azure](../media/waf-azure-policy/assign-policy.png)

4.  Na kartě základy stránky pro přiřazení zásad aktualizujte následující pole:
    1.  **Rozsah**: vyberte, na jaké Předplatné a skupiny prostředků Azure by měl mít vliv Azure Policy.
    2.  **Vyloučení**: vyberte všechny prostředky z oboru, které se mají vyloučit ze zásad. 
    3.  **Definice zásady**: vyberte Azure Policy, který se má použít u oboru s vyloučeními. Na panelu hledání zadejte "Firewall webových aplikací" a vyberte odpovídající Azure Policy firewallu webových aplikací.

![Firewall webových aplikací Azure](../media/waf-azure-policy/policy-listings.png)


5.  Vyberte kartu **parametry** a aktualizujte parametry zásad. Pro lepší objasnění toho, co parametr dělá, najeďte myší na ikonu informace vedle názvu parametru pro další objasnění.

6.  Vyberte **zkontrolovat + vytvořit** a dokončete zásady Azure. Azure Policy bude trvat přibližně 15 minut, než bude aktivní pro nové prostředky.
