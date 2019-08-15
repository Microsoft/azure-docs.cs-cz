---
title: Jak používat balíček obsahu Azure Active Directory Power BI Content Pack | Dokumentace Microsoftu
description: Naučte se používat balíček obsahu Azure Active Directory Power BI Content Pack.
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d56802e96028b6b01b6be749405c56df2648161
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988239"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Použití balíčku obsahu Azure Active Directory Power BI

|  |
|--|
|V současné době balíček obsahu Azure AD Power BI používá rozhraní Azure AD Graph API k načtení dat z vašeho tenanta Azure AD. V důsledku toho můžete zaznamenat určitý nesoulad mezi daty, která jsou k dispozici v balíčku obsahu, a daty načtenými pomocí [rozhraní Microsoft Graph API pro generování sestav](concept-reporting-api.md). |
|  |

Power BI balíček obsahu pro Azure Active Directory (Azure AD) obsahuje předem připravené sestavy, které vám pomůžou pochopit, jak uživatelé přijímají a používají funkce služby Azure AD. Díky tomu můžete získat přehled o všech aktivitách v adresáři pomocí prostředí s bohatou vizualizací v Power BI. Můžete také vytvořit vlastní řídicí panel a sdílet ho s kýmkoli ve vaší organizaci. 

## <a name="prerequisites"></a>Požadavky

K používání balíčku obsahu potřebujete licenci Azure AD Premium (P1/P2). Pokud chcete upgradovat edici Azure Active Directory, přečtěte si téma [Začínáme se Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) .

## <a name="install-the-content-pack"></a>Instalace balíčku obsahu

Projděte si [rychlý Start](quickstart-install-power-bi-content-pack.md) a nainstalujte balíček obsahu Power BI Azure AD.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Sestavy zahrnuté v této verzi balíčku obsahu protokolů služby Azure AD

Do balíčku obsahu Power BI služby Azure AD jsou zahrnuty následující sestavy. Sestavy obsahují data za **posledních 30 dní**.

**Sestava o využití a trendech aplikací**:  Tato sestava poskytuje přehled o aplikacích používaných ve vaší organizaci. Můžete získat seznam nejoblíbenějších aplikací nebo zjistit, jak se používá aplikace, kterou jste nedávno zavedli ve vaší organizaci. Díky tomu můžete sledovat a zdokonalovat využití v průběhu času.

**Přihlášení podle umístění a uživatelů**: Tato sestava poskytuje data o všech přihlášeních provedených pomocí Azure identity. Pomocí této sestavy můžete přejít k jednotlivým přihlašovacím údajům a odpovědím na otázky, jako jsou:

- Kam se uživatel přihlásil?
- Který uživatel má nejvíce přihlášení a odkud se přihlašuje? 
- Bylo přihlášení úspěšné?  
 
Výsledky můžete filtrovat také výběrem konkrétního data nebo umístění.

**Jedineční uživatelé na aplikaci**:  Tato sestava poskytuje zobrazení všech jedinečných uživatelů, kteří používají danou aplikaci. Zahrnuje jenom uživatele, kteří mají*úspěšně*přihlášení do aplikace.

**Přihlášení zařízení**: Tato sestava vám pomůže pochopit různé profily zařízení používané ve vaší organizaci a určovat zásady zařízení na základě využití. Poskytuje data kolem typu operačního systému a prohlížečů používaných k přihlašování k aplikacím spolu s podrobnými informacemi o uživatelích, včetně:

- Uživatelské jméno
- IP adresa
- Location 
- Stav přihlášení 

**SSPR trychtýř**: Tato sestava vám pomůže pochopit, jak se nástroj SSPR používá v rámci vaší organizace. Pomocí nástroje SSPR můžete zobrazit, kolik z nich bylo pokusy o obnovení hesla, a kolik z nich bylo úspěšné. Můžete se také dig hlouběji na chyby resetování hesla a pochopit, proč došlo k určitým chybám. 

## <a name="customize-azure-ad-activity-content-pack"></a>Přizpůsobení balíčku obsahu aktivit Azure AD

**Změna vizualizace**:  Vizualizaci sestavy můžete změnit kliknutím na **Upravit sestavu** a výběrem požadované vizualizace.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/09.png) 
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/10.png) 

**Zahrnout další pole**:  Do sestavy můžete přidat pole nebo je odebrat výběrem vizuálu, ke kterému chcete pole Přidat nebo odebrat. Můžete například přidat pole "stav přihlášení" do zobrazení tabulky, jak je znázorněno níže. 
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/11.png) 

**Připnout vizualizace na řídicí panel**:  Řídicí panel můžete přizpůsobit tak, že zahrnete do sestavy vlastní vizualizace a připnete ji na řídicí panel. 

![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/13.png) 
 
**Sdílení řídicího panelu**: Řídicí panel můžete sdílet také s uživateli ve vaší organizaci. Po sdílení sestavy uživatelé uvidí pole, která jste vybrali v sestavě.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Naplánování denní aktualizace sestavy Power BI

Chcete-li naplánovat každodenní aktualizaci sestavy Power BI, použijte**Nastavení** > **naplánovat aktualizaci** **sady datových sad** > a nastavte ji tak, jak je uvedeno níže.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Aktualizace na novější verzi balíčku obsahu

Pokud chcete balíček obsahu aktualizovat na novější verzi:

- Stáhněte si nový balíček obsahu a nastavte ho podle pokynů v tomto článku.

- Po nastavení přejděte na**Nastavení** >  **zdroje** > dat**přihlašovací údaje ke zdroji dat** a znovu zadejte svoje přihlašovací údaje.

    ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/16.png) 

Jakmile ověříte, že nová verze balíčku obsahu funguje podle očekávání, můžete v případě potřeby odebrat starou verzi tím, že odstraníte základní sestavy a datové sady spojené s tímto balíčkem obsahu.

## <a name="troubleshoot-content-pack-errors"></a>Řešení chyb balíčku obsahu

Při práci s balíčkem obsahu je možné spustit následující chyby: 

- [Neúspěšná aktualizace](#refresh-failed) 
- [Nepovedlo se aktualizovat přihlašovací údaje zdroje dat.](#failed-to-update-data-source-credentials) 
- [Import dat trvá příliš dlouho.](#data-import-is-too-slow) 

Obecnou nápovědu k Power BI najdete v těchto [článcích nápovědy](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

### <a name="refresh-failed"></a>Aktualizace se nezdařila 
 
**Jak je tato chyba Surface**: Odeslání e-mailu z Power BI nebo neúspěšného stavu v historii aktualizace. 


| Příčina | Jak opravit |
| ---   | ---        |
| Chyby při selhání aktualizace se můžou způsobovat při resetování přihlašovacích údajů uživatelů, kteří se připojili k balíčku obsahu, ale ne aktualizovat v nastavení připojení balíčku obsahu. | V Power BI Najděte datovou sadu odpovídající řídicímu panelu protokoly aktivit Azure AD (**protokoly aktivit Azure Active Directory**), zvolte naplánovat aktualizaci a zadejte svoje přihlašovací údaje služby Azure AD. |
| Aktualizace může selhat kvůli problémům s daty v podkladovém balíčku obsahu. | [Soubor a lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
### <a name="failed-to-update-data-source-credentials"></a>Nepovedlo se aktualizovat přihlašovací údaje zdroje dat. 
 
**Jak je tato chyba Surface**: Když se v Power BI připojíte k balíčku obsahu protokol aktivit služby Azure AD. 

| Příčina | Jak opravit |
| ---   | ---        |
| Připojující se uživatel není globálním správcem nebo čtenářem zabezpečení nebo správcem zabezpečení. | Pro přístup k balíčkům obsahu použijte účet, který je buď globálním správcem, nebo čtenářem zabezpečení, nebo správcem zabezpečení. |
| Váš tenant není tenant úrovně Premium nebo nemá aspoň jednoho uživatele se souborem licence Premium. | [Soubor a lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
### <a name="data-import-is-too-slow"></a>Import dat je příliš pomalý. 
 
**Jak je tato chyba Surface**: Po připojení balíčku obsahu v Power BI začne proces importu dat připravovat váš řídicí panel pro protokoly aktivit služby Azure AD. Zobrazí se tato zpráva: **Importují se data...** bez dalšího postupu.  

| Příčina | Jak opravit |
| ---   | ---        |
| V závislosti na velikosti vašeho tenanta může tento krok trvat několik minut až 30 minut. | Pokud se zpráva nezměnila tak, aby zobrazovala řídicí panel během celé hodiny, požádejte [o pomoc lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>Další postup

* [Nainstalujte balíček obsahu Power BI](quickstart-install-power-bi-content-pack.md).
* [Co jsou sestavy Azure AD?](overview-reports.md).
