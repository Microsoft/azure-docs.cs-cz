---
title: Jak používat balíček obsahu Azure Active Directory Power BI Content Pack | Dokumentace Microsoftu
description: Naučte se používat balíček obsahu Azure Active Directory Power BI Content Pack.
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 16026adc2eb0179cd2b42f449494cbbc6547b946
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651448"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Jak používat balíček obsahu Azure Active Directory Power BI

|  |
|--|
|V současné době balíček obsahu Azure AD Power BI používá rozhraní Azure AD Graph API k načtení dat z vašeho tenanta Azure AD. V důsledku toho můžete zaznamenat určitý nesoulad mezi daty, která jsou k dispozici v balíčku obsahu, a daty načtenými pomocí [rozhraní Microsoft Graph API pro generování sestav](concept-reporting-api.md). |
|  |

Power BI content pack pro službu Azure Active Directory (Azure AD) obsahuje předdefinované sestavy, které vám pomohou pochopit, jakým způsobem uživatelé přijímají a používat funkce služby Azure AD. To umožňuje získat přehled o všech aktivitách v adresáři, pomocí prostředí bohatých funkcí vizualizace v Power BI. Můžete také vytvořit vlastní řídicí panel a sdílet ho s kýmkoli ve vaší organizaci. 

## <a name="prerequisites"></a>Požadavky

Potřebujete licenci Azure AD premium (P1/P2) pro použití balíčku obsahu. Zobrazit [Začínáme se službou Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) upgradovat edici Azure Active Directory.

## <a name="install-the-content-pack"></a>Nainstalujte balíček obsahu

Podívejte se [rychlý Start](quickstart-install-power-bi-content-pack.md) k instalaci balíčku obsahu Azure AD Power BI.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Balíčku obsahu protokolů sestavy zahrnuté v této verzi služby Azure AD

Tyto sestavy jsou součástí balíčku obsahu Azure AD Power BI. Sestavy obsahují data z **posledních 30 dní**.

**Sestava využití aplikace a trendy**:  Tato sestava poskytuje informace o aplikacích používaných ve vaší organizaci. Můžete získat seznam nejoblíbenějších aplikací nebo pochopit, jak se používá aplikace, které se nedávno zavedli ve vaší organizaci. To umožňuje sledovat a vylepšit využití v průběhu času.

**Přihlášení podle umístění a uživatelů**: Tato sestava poskytuje data o všech přihlášeních provedených pomocí Azure Identity. U této sestavy může se ponořit až individuální přihlášení a odpovězte na otázky jako:

- Kde tento uživatel přihlásit z?
- Který uživatel má nejvíce přihlášení a odkud se přihlašuje? 
- Bylo přihlášení úspěšné?  
 
Výsledky můžete také filtrovat tak, že vyberete konkrétní datum nebo umístění.

**Jedineční uživatelé na aplikaci**:  Tato sestava poskytuje přehled všech jedinečných uživatelů, kteří danou aplikaci. Zahrnuje pouze uživatelé, kteří mají "*úspěšně*" přihlášení do aplikace.

**Přihlášení zařízení**: Tato sestava vám pomůže pochopit různé profily zařízení používané ve vaší organizaci a určit zásady zařízení na základě využití. Poskytuje daty o typ operačního systému a používaných pro přihlášení k aplikacím, spolu s podrobnými informacemi o uživatelích, včetně prohlížečů:

- Uživatelské jméno
- IP adresa
- Umístění 
- Stav přihlášení 

**Trychtýř SSPR**: Tato sestava pomáhá pochopit, jak se používá nástroj pro samoobslužné resetování HESLA ve vaší organizaci. Můžete zobrazit, kolik o resetování hesla bylo prostřednictvím nástroje SSPR a kolik z nich byly úspěšné. Můžete také podrobnější informace o selhání resetování hesel a pochopit, proč k některým chybám došlo. 

## <a name="customize-azure-ad-activity-content-pack"></a>Přizpůsobení balíčku obsahu aktivit služby Azure AD

**Změna vizualizace**:  Vizualizaci sestavy můžete změnit kliknutím **upravit sestavu** a vyberte požadované vizualizace.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/09.png) 
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/10.png) 

**Zahrnutí dalších polí**:  Můžete přidat pole do sestavy nebo z ní odebrat výběrem vizuálu, do které chcete přidat nebo odebrat pole. Můžete například přidat pole "stav přihlášení" do zobrazení tabulky, jak je znázorněno níže. 
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/11.png) 

**Připnutí vizualizace na řídicí panel**:  Řídicí panel můžete přizpůsobit tak, že včetně vaší vlastní vizualizace do sestavy a Připnutí na řídicí panel. 

![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/13.png) 
 
**Sdílení řídicího panelu**: Řídicí panel můžete také sdílet s uživateli ve vaší organizaci. Jakmile sestavu sdílíte, uživatelé mohou vidět vybraných v sestavě polí.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Naplánovat každodenní aktualizaci sestavy Power BI

Pokud chcete naplánovat každodenní aktualizaci sestavy Power BI, přejděte na **datových sad** > **nastavení** > **naplánovat aktualizaci** a nastavte ji podle příkladu níže.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Aktualizovat na novější verzi balíčku obsahu

Pokud chcete aktualizovat svůj balíček obsahu na novější verzi:

- Stáhněte si nový balíček obsahu a nastavení podle pokynů v tomto článku.

- Jakmile nastavíte ho, přejděte na **zdroj dat** > **nastavení** > **přihlašovací údaje ke zdroji dat** a znovu zadejte svoje přihlašovací údaje.

    ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/16.png) 

Jakmile ověříte, že nová verze balíčku obsahu funguje podle očekávání, můžete odebrat starou verzi v případě potřeby odstraněním podkladových sestav a datové sady přidružené k tomuto balíčku obsahu.

## <a name="still-having-issues"></a>Pořád máte problémy? 

Podívejte se na [průvodce odstraňováním potíží](troubleshoot-content-pack.md). Obecnou nápovědu k Power BI najdete v těchto [článcích nápovědy](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
 
 
## <a name="next-steps"></a>Další postup

* [Nainstalujte balíček obsahu Power BI](quickstart-install-power-bi-content-pack.md).
* [Řešení potíží s chybami balíčku obsahu](troubleshoot-content-pack.md).
* [Co jsou sestavy Azure AD? ](overview-reports.md).
