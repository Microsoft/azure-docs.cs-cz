---
title: "Integrace s integrací protokolů Azure výstrahy Azure Security Center | Microsoft Docs"
description: "Tento článek vám pomůže začít pracovat s integrací výstrahy Security Center s integrací protokolů Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: barclayn
ms.openlocfilehash: 229f83d4d74837e506c30a0fd8c704da2b7dc12a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration"></a>Integrace Azure Security Center výstrahy s integrací protokolů Azure
Mnoho operace zabezpečení a reakce na incidenty týmy závisí na informace o zabezpečení a událostí správy (SIEM) řešení jako výchozí bod pro triaging a prošetřování výstrah zabezpečení. Díky integraci protokolu Azure můžete integrovat Azure Security Center výstrahy s vaším řešením SIEM.

Integrace protokolů Azure aktuálně podporuje HP ArcSight, Splunk a IBM QRadar.

## <a name="what-logs-can-i-integrate"></a>Jaké protokoly můžete integrovat?
Azure vytvoří rozsáhlé protokolování pro každou službu. Tyto protokoly jsou klasifikovány jako:

* **Řízení nebo protokoly** , poskytují přehled o činnosti Azure Resource Manager vytvoření, aktualizace a odstranění. Tyto události rovině řízení jsou prezentované v protokolech aktivity Azure
* **Datové roviny protokoly** , poskytují přehled o události vyvolané při použití prostředek služby Azure. Příkladem je protokol událostí systému Windows, kde můžete získat informace o události zabezpečení z prohlížeče událostí zabezpečený kanál. Azure diagnostické protokoly jsou prezentované dat roviny událostí (které jsou generované virtuálního počítače nebo služby Azure).

Integrace protokolů Azure aktuálně podporuje integraci:

* Azure protokoly virtuálních počítačů
* Protokoly auditu Azure
* Výstrahy Azure Security Center

## <a name="install-azure-log-integration"></a>Nainstalujte integrační protokolů Azure
Stáhněte si [integrace Azure protokolu](https://www.microsoft.com/download/details.aspx?id=53324).

Služba integrace Azure protokolu shromažďuje telemetrická data z počítače, na kterém je nainstalována.  Mezi shromažďovaná telemetrická data je:

* Výjimky, ke kterým došlo během provádění integrace protokolů Azure
* Metriky o počtu dotazy a zpracování událostí
* Statistiky, o které Azlog.exe jsou používány možnosti příkazového řádku

> [!NOTE]
> Zrušením zaškrtnutí políčka tuto možnost můžete vypnout kolekce telemetrická data.
>
>

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrovat výstrahy a protokolování auditu Azure Security Center
1. Otevřete příkazový řádek a **cd** do **c:\Program Files\Microsoft Azure protokolu integrace**.
2. Spustit **azlog createazureid** příkaz pro vytvoření [Azure Active Directory Service Principal](../active-directory/active-directory-application-objects.md) v klientech služby Azure Active Directory (AD), které jsou hostiteli předplatná Azure.

    Budete vyzváni k přihlášení Azure.

   > [!NOTE]
   > Musí být vlastníkem předplatného nebo Spolusprávce předplatného.
   >
   >

    Ověřování do Azure se provádí prostřednictvím služby Azure AD.  Vytvoření objektu služby pro integraci protokolů Azure vytvoří Azure AD identity, který je přiřazen přístup ke čtení z předplatných Azure.
3. Spustit **azlog Autorizovat <SubscriptionID>**  příkaz přiřazení přístupu čtečky u předplatného k objektu služby vytvořené v kroku 2. Pokud nezadáte **SubscriptionID**, pak objekt služby je přiřadit role Čtenář pro všechna předplatná, ke kterým máte přístup.

   > [!NOTE]
   > Mohou se zobrazit upozornění, pokud spustíte **Autorizovat** příkaz ihned po **createazureid** příkaz. Některé latence mezi při vytvoření účtu Azure AD a účet je k dispozici pro použití neexistuje. Pokud Počkejte asi 10 sekund, po spuštění **createazureid** příkaz ke spuštění **Autorizovat** příkaz, pak byste neměli vidět tato upozornění.
   >
   >
4. Zkontrolujte následující složky na potvrďte, že jsou soubory JSON protokolů auditu existuje:

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. Zkontrolujte následující složky pro potvrzení, že výstrahy Security Center existuje v nich:

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. Nakonfigurujte konektor systému SIEM souboru předávání do příslušné složky. Postup se liší podle SIEM, kterou používáte.

## <a name="next-steps"></a>Další postup
Další informace o Azure protokoly aktivity a definice vlastností najdete v tématu:

* [Audit operací pomocí Resource Manageru](../azure-resource-manager/resource-group-audit.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reakce na výstrahy zabezpečení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější informace zabezpečení Azure a informace.
