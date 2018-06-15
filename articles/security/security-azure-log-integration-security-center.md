---
title: Protokol integrace se službou Azure s security center | Microsoft Docs
description: Další informace o získání Azure Security center výstrahy práce integrace protokolu
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9acce21d544a43adcd0c0983771c02c6bb39caec
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29138494"
---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>Jak získat vaše výstrahy Security Center v integraci protokolů Azure

Tento článek obsahuje kroky potřebné k povolení integrace se službou Azure protokolu službu, kterou chcete vyžádat zabezpečení výstrahy generované Azure Security Center. Musíte úspěšně jste dokončili kroky v [Začínáme](security-azure-log-integration-get-started.md) článek před provedením kroků v tomto článku.

## <a name="detailed-steps"></a>Podrobné kroky

Následující kroky vytvoří požadované objektu služby Azure Active Directory a přiřadit zásady služby oprávnění ke čtení pro předplatné:
1. Otevřete příkazový řádek a přejděte do **c:\Program Files\Microsoft Azure protokolu integrace**
2. Spusťte příkaz``azlog createazureid``

    Tento příkaz zobrazí výzvu k přihlášení Azure. Příkaz vytvoří [Azure Active Directory Service Principal](../active-directory/develop/active-directory-application-objects.md) v klienty Azure AD, které jsou hostiteli předplatná Azure, ve kterých přihlášený uživatel je správce, Spolusprávce nebo vlastníka. Příkaz se nezdaří, pokud je přihlášený uživatel pouze uživatel Guest v klientovi Azure AD. Ověřování do Azure se provádí prostřednictvím Azure Active Directory (AD). Vytvoření objektu služby pro integraci Azlog vytvoří Azure AD identity, která bude mít přístup ke čtení z předplatných Azure.

3. Potom spustíte příkaz, který přiřazuje přístup čtečky u předplatného hlavní služby, kterou jste právě vytvořili. Pokud nezadáte ID předplatného, příkaz se pokusí přiřadit roli služby hlavní čtečky pro všechna předplatná, ke které máte přístup. </br></br>
``azlog authorize <SubscriptionID>`` </br> například </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    Okamžitě po spuštění příkazu autorizovat, mohou se zobrazit upozornění ```createazureid``` příkaz. Některé latence mezi při vytvoření účtu Azure AD a účet je k dispozici pro použití neexistuje. Pokud Počkejte přibližně 60 sekund po spuštění ```createazureid``` příkaz ke spuštění příkazu autorizovat, pak byste neměli vidět tato upozornění.

4. Zkontrolujte následující složky na potvrďte, že jsou soubory JSON protokolů auditu existuje:
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. Zkontrolujte následující složky pro potvrzení, že výstrahy Security Center existuje v nich:</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

Pokud narazíte na potíže během instalace a konfigurace, otevřete prosím [žádost o podporu](/azure-supportability/how-to-create-azure-support-request.md), vyberte **integrace protokolu** jako službu, pro kterou jsou žádosti o podporu.

## <a name="next-steps"></a>Další postup
Další informace o protokolu integrace se službou Azure, najdete v následujících dokumentech:

* [Microsoft Azure protokolu integrace pro Azure protokoly](https://www.microsoft.com/download/details.aspx?id=53324) – Download Center podrobnosti, požadavky na systém a instalovat pokyny týkající se integrace protokolů Azure.
* [Úvod do integrace Azure protokolu](security-azure-log-integration-overview.md) – tento dokument vás seznámí s integrace Azure protokolu, jejích klíčových funkcích a jak to funguje.
* [Partner kroky konfigurace](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – tento příspěvek blogu ukazuje, jak nakonfigurovat integraci Azure protokolu pro práci s partnerských řešení Splunk, HP ArcSight a IBM QRadar.
* [Protokolů Azure integrace nejčastější dotazy (FAQ)](security-azure-log-integration-faq.md) – nejčastější dotazy týkající se tento odpovědi dotazy týkající se integrace protokolů Azure.
* [Nové funkce pro protokoly auditu Azure a Azure diagnostics](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – tento příspěvek blogu vás seznámí s protokoly auditu Azure a další funkce, které vám pomůžou získat přehled o činnosti vašich prostředků Azure.
