---
title: Konfigurace zabezpečení pro přístup k a spravovat Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje postup konfigurace zabezpečení a oprávnění jako přístup pro správu zásad a přístupu k datům zásady pro zabezpečení služby Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: c9bddf8e20524433b31793e277efd954a5d1320e
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423370"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Udělení přístupu k datům prostředí Time Series Insights pomocí webu Azure Portal

Tento článek popisuje dva typy zásad přístupu Time Series Insights.

## <a name="video"></a>Video: 

### <a name="in-this-video-we-cover-creating-and-managing-access-policies-within-time-series-insights-br"></a>V tomto videu se budeme zabývat vytváření a Správa zásad přístupu v rámci služby Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/5zTIdyHMJW8]

V prostředích Time Series Insights jsou dva nezávislé typy zásad přístupu:

* Zásady přístupu ke správě
* Zásady přístupu k datům

Oba typy zásad udělují objektům zabezpečení Azure Active Directory (uživatelům a aplikacím) různá oprávnění ke konkrétnímu prostředí. Objekty zabezpečení (uživatelé a aplikace) musí patřit do active directory (označuje se jako Azure tenant) přidružené k předplatnému, které obsahuje prostředí.

Zásady přístupu ke správě udělují oprávnění související s konfigurací prostředí, jako je například
*   vytvoření nebo odstranění prostředí, zdrojů událostí nebo referenčních datových sad a
*   správa zásad přístupu k datům.

Zásady přístupu k datům udělují oprávnění k vydávání dotazů na data, zpracování referenčních dat v rámci prostředí a sdílení uložených dotazů a perspektiv přidruženým k danému prostředí.

Tyto dva typy zásad umožňují jasné oddělení přístupu ke správě prostředí od přístupu k datům v prostředí. Například je možné nastavit prostředí tak, aby vlastník nebo Tvůrce prostředí se odebere přístup k datům. Kromě toho uživatele a služby, které můžou číst data z prostředí může udělit přístup ke konfiguraci prostředí.

## <a name="grant-data-access"></a>Udělení přístupu k datům
Použijte následující postup udělení přístupu k datům pro uživatele instančního objektu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte prostředí Time Series Insights. Typ **časové řady** v **hledání** pole. Vyberte **prostředí Time Series Insights** ve výsledcích hledání. 

3. Ze seznamu vyberte vaše prostředí Time Series Insights.
   
4. Vyberte **zásady přístupu k datům**a pak vyberte **+ přidat**.
  ![Správa zdroje Time Series Insights – prostředí](media/data-access/getstarted-grant-data-access1.png)

5. Vyberte **vybrat uživatele**.  Vyhledání uživatelského jména nebo e-mailové adresy k vyhledání uživatele, který chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr. 

   ![Správa zdroje Time Series Insights – přidat](media/data-access/getstarted-grant-data-access2.png)

6. Vyberte **vybrat roli**. Zvolte roli odpovídající přístup pro uživatele:
   - Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky, které jsou uloženy dotazů a perspektiv s ostatními uživateli prostředí. 
   - V opačném případě vyberte **čtečky** povolení data dotazu uživatele v prostředí a ukládání osobních (ne sdílených) dotazů v prostředí.

   Vyberte **Ok** k potvrzení volby role.

   ![Správa zdroje Time Series Insights – vybrat uživatele](media/data-access/getstarted-grant-data-access3.png)

8. Vyberte **Ok** v **vybrat roli uživatele** stránky.

   ![Správa zdroje Time Series Insights – vybrat roli](media/data-access/getstarted-grant-data-access4.png)

9. **Zásady přístupu k datům** stránka obsahuje seznam uživatelů a rolí pro každého uživatele.

   ![Správa zdroje Time Series Insights – výsledky](media/data-access/getstarted-grant-data-access5.png)

## <a name="provide-guest-access-to-a-user-from-another-aad-tenant"></a>Poskytnout přístup hosta pro uživatele z jiného tenanta AAD

Správa rolí; není "Guest" To je termín používaný pro účet, který je pozval z jednoho tenanta do jiného. Jakmile účet hosta má pozvánku do adresáře tenantovi, může mít stejné řízení přístupu u stejně jako jakýkoli jiný účet, udělit přístup ke správě služby TSI prostředí pomocí okna řízení přístupu (IAM) nebo udělit přístup k datům v prostředí prostřednictvím okna zásady přístupu k datům. Další informace o přístupu hostů tenanta AAD, najdete v tomto [dokumentu](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Použijte následující postup udělení přístupu hostů k prostředí Time Series Insights do AAD uživatele z jiného tenanta:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte prostředí Time Series Insights. Typ **časové řady** v **hledání** pole. Vyberte **prostředí Time Series Insights** ve výsledcích hledání.

3. Ze seznamu vyberte vaše prostředí Time Series Insights.

4. Vyberte **zásady přístupu k datům**a pak vyberte + **pozvat**.

    ![Správa zdroje Time Series Insights – pozvání uživatele](media/data-access/getstarted-grant-data-access6.png)

5. Zadejte e-mail uživatele, který chcete pozvat. Poznámka: mělo že by to být e-mailu přidružené k AAD. Může volitelně zahrnovat e-mailové pozvánce osobní zprávu.

    ![Správa zdroje Time Series Insights – vybrat uživatele](media/data-access/getstarted-grant-data-access7.png)

6. Měli byste vidět bublinu potvrzení na obrazovce.

    ![Správa zdroje Time Series Insights – potvrzení uživatele](media/data-access/getstarted-grant-data-access8.png)

7. Vyberte **vybrat uživatele**. Hledání e-mailová adresa uživatele typu Host, jenom pozval vyhledejte uživatele, kterého chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr.
  
    ![Správa zdroje Time Series Insights – potvrzení uživatele](media/data-access/getstarted-grant-data-access9.png)

8. Vyberte **vybrat roli**. Zvolte odpovídající přístup role pro uživatele typu Host:

    * Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky, které jsou uloženy dotazů a perspektiv s ostatními uživateli prostředí.

    * V opačném případě vyberte **čtečky** povolení data dotazu uživatele v prostředí a ukládání osobních (ne sdílených) dotazů v prostředí.

    Vyberte **Ok** k potvrzení volby role.

    ![Správa zdroje Time Series Insights – vybrat roli](media/data-access/getstarted-grant-data-access10.png)

9. Vyberte **Ok** v **vybrat roli uživatele** stránky.

10. **Zásady přístupu k datům** stránka nyní obsahuje uživatele typu Host a rolím pro každého uživatele typu Host.

    ![Správa zdroje Time Series Insights – potvrďte role](media/data-access/getstarted-grant-data-access11.png)

11. Teď uživatel typu Host bude potřeba provést určité pouze kroky pro přístup k prostředí v Azure můžete tenanta pozvat jim. Nejprve je třeba přijmout pozvánku, kterou jste právě odeslali. Tato pozvánka dala přijde e-mailem na e-mailovou adresu, které vás pozvat v kroku 5. Měli byste kliknout na "Začínáme," potvrďte.

    ![Správa zdroje Time Series Insights – pozvání uživatele](media/data-access/getstarted-grant-data-access12.png)

12. V dalším kroku uživatele typu Host muset přijmout oprávnění spojená s správce organizace.

    ![Správa zdroje Time Series Insights – oprávnění](media/data-access/getstarted-grant-data-access13.png)

13. Když uživatel typu Host se zaznamená do e-mailovou adresu zveme a nepřijme pozvání, bude přejít k insights.azure.com. Jednou, se musí kliknout na avatara u e-mailu v pravém horním rohu obrazovky. 

    ![Správa zdroje Time Series Insights – oprávnění](media/data-access/getstarted-grant-data-access14.png)

14. V dalším kroku bude uživatel typu Host vyberte Azure tenanta z rozevírací nabídky adresáře. Toto je tenant, který je pozval. 

    ![Správa zdroje Time Series Insights – oprávnění](media/data-access/getstarted-grant-data-access15.png)

15. Nakonec když uživatel typu Host vybere vašeho tenanta, zobrazí se prostředí Time Series Insights, který jste právě zadali jim přístup k. By teď měly mít všechny možnosti, které jsou přidružené k roli, kterou jste zadali v kroku 8.

## <a name="next-steps"></a>Další postup
* Přečtěte si [přidání zdroje událostí centra událostí do prostředí Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.
* Zobrazte své prostředí v [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com).
