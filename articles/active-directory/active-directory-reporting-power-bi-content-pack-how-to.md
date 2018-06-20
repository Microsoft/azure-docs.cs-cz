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
ms.component: compliance-reports
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 531d192bf024def1a90d9e5034645b6ec6e0c0e1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225418"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Jak používat balíček obsahu Azure Active Directory Power BI Content Pack

Pro vás jako správce IT je naprosto nezbytné pochopit, jakým způsobem uživatelé přijímají a používají funkce služby Azure Active Directory. Umožní vám to plánovat infrastrukturu IT a komunikaci za účelem zvýšení míry využití a získání maxima z funkcí AAD. Balíček obsahu Power BI pro Azure Active Directory vám umožňuje podrobněji analyzovat data, abyste pochopili, jakým způsobem je můžete používat k získání lepšího přehledu o tom, jak služba Azure Active Directory funguje s různými možnostmi, na které spoléháte.  Integrace rozhraní Azure Active Directory API do Power BI umožňuje snadné stažení předem vytvořených balíčků obsahu a získání přehledu o veškerých aktivitách v rámci služby Azure Active Directory pomocí bohatých funkcí vizualizace, které Power BI nabízí. Můžete si vytvořit vlastní řídicí panel a snadno ho sdílet se všemi uživateli ve vaší organizaci. 

Toto téma obsahuje podrobné pokyny pro instalaci a použití balíčku obsahu ve vašem prostředí.

## <a name="installation"></a>Instalace  

**Instalace balíčku obsahu Power BI:**

1. Přihlaste se k [Power BI](https://app.powerbi.com/groups/me/getdata/services) pomocí svého účtu Power BI (jedná se o stejný účet jako pro O365 nebo Azure AD).

2. V dolní části levého navigačního podokna vyberte **Získat data**.

    ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/01.png)
 
3. V okně **Služby** klikněte na **Získat**.
   
    ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/02.png)

4.  Vyhledejte **Azure Active Directory**.

    ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/03.png)
 
5.  Po zobrazení výzvy zadejte ID klienta služby Azure AD a pak klikněte na **Další**.

    > [!TIP] 
    > ID tenanta Office 365 nebo Azure AD můžete rychle získat tak, že se přihlásíte k portálu Azure AD, přejdete do adresáře a ze [stránky **Vlastnosti**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) zkopírujete **ID adresáře**.

    ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/04.png) 

6.  Klikněte na **Přihlášení**. 
 
    ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/05.png) 



7.  Zadejte uživatelské jméno a heslo a pak klikněte na **Přihlášení**.
 
    ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/06.png) 

8.  V dialogovém okně souhlasu aplikace klikněte na **Přijmout**.
 
9.  Jakmile řídicí panel protokolů aktivity služby Azure Active Directory vytvoříte, klikněte na něj.
 
    ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/08.png) 

## <a name="what-can-i-do-with-this-content-pack"></a>Co můžu s tímto balíčkem obsahu dělat?

Než se začneme zabývat tím, co s tímto balíčkem obsahu můžete dělat, podívejme se na rychlý přehled různých sestav, které balíček obsahu obsahuje. Data sestavy se týkají **posledních 30 dní**.

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>Sestavy zahrnuté v této verzi balíčku obsahu protokolů služby Azure Active Directory

**Sestava využití aplikací a trendů**: Získejte přehled o aplikacích používaných ve vaší organizaci a o tom, které se používají nejvíce a kdy. Tato sestava slouží k získání přehledu o tom, jak se aplikace, kterou jste ve vaší organizaci nedávno zavedli, používá, nebo ke zjištění oblíbených aplikací. Umožní vám vylepšit využití v případě, že vidíte, že se aplikace nepoužívá.

**Přihlášení podle umístění a uživatelů**: Získejte přehled o veškerých přihlášeních provedených pomocí Azure Identity a přehled o identitách uživatelů. Můžete podrobněji analyzovat individuální přihlášení a odpovědět si na otázky jako:

- Odkud se tento uživatel přihlásil?
- Který uživatel má nejvíce přihlášení a odkud se přihlašuje? 
- Bylo přihlášení úspěšné?  
 
Podrobnosti můžete rozbalit kliknutím na konkrétní datum nebo umístění.

**Jedineční uživatelé na aplikaci**: Získejte přehled všech jedinečných uživatelů, kteří danou aplikaci využívají. Patří sem pouze uživatelé, kteří se k aplikaci *úspěšně* přihlásili.

**Přihlášení pomocí zařízení**: Získejte přehled o typu operačního systému a prohlížečích používaných uživateli ve vaší organizaci s podrobnými informacemi o uživatelích, včetně:

- Uživatelské jméno
- IP adresa
- Umístění 
- Stav přihlášení 

Tato sestava vám pomůže pochopit různé profily zařízení používané ve vaší organizaci a určit zásady zařízení podle toho, co se používá.

**Trychtýř SSPR**: Získejte představu o tom, jakým způsobem se ve vaší organizaci resetují hesla. Zjistěte, kolik pokusů o resetování hesla bylo provedeno prostřednictvím nástroje SSPR a kolik z nich bylo úspěšných. Podívejte se podrobněji na chyby při resetování hesel pomocí trychtýře SSPR a zjistěte, proč k některým chybám došlo. Tato sestava umožňuje lépe pochopit, jakým způsobem se nástroj SSPR ve vaší organizaci používá. Umožní vám pak správně se rozhodovat.

## <a name="customizing-azure-ad-activity-content-pack"></a>Přizpůsobení balíčku obsahu aktivit služby Azure AD

**Změna vizualizace**: Vizualizaci sestavy můžete změnit kliknutím na **Upravit sestavu** a výběrem požadované vizualizace.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/09.png) 
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/10.png) 

**Zahrnutí dalších polí**: Pole můžete do sestavy přidat nebo ho z ní odebrat výběrem vizuálu, do kterého chcete pole přidat nebo z kterého ho chcete odebrat. V následujícím příkladu se přidává pole „stav přihlášení“ do zobrazení tabulky. 
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/11.png) 

**Připnutí vizualizací na řídicí panel**: Řídicí panel můžete přizpůsobit, do sestavy můžete zahrnout vlastní vizualizace a sestavu pak připnout na řídicí panel. V následujícím příkladu se přidává nový filtr s názvem „stav přihlášení“ a zahrnuje se do sestavy. Došlo také ke změně vizualizace z pruhového grafu na spojnicový. Tento nový vizuál se dá připnout na řídicí panel.

![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/12.png) 

![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/13.png) 
 

 


**Sdílení řídicího panelu**: Jakmile vytvoříte požadovaný obsah, můžete řídicí panel sdílet s uživateli ve vaší organizaci. Mějte prosím na paměti, že jakmile sestavu sdílíte, můžou uživatelé vidět pole, která jste v sestavě vybrali.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>Plánování denní aktualizace sestavy Power BI

Pokud chcete naplánovat každodenní aktualizaci sestavy Power BI, přejděte na **Datové sady > Nastavení > Naplánovat aktualizaci** a nastavte ji podle příkladu níže.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>Aktualizace na novější verzi balíčku obsahu

Postup aktualizace balíčku obsahu na novější verzi:

- Stáhněte si nový balíček obsahu a nastavte ho podle pokynů uvedených v tomto článku.

- Jakmile ho nastavíte, přejděte na **Zdroj dat > Nastavení > Přihlašovací údaje ke zdroji dat** a znovu zadejte přihlašovací údaje, jak je uvedeno níže.

    ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/active-directory-reporting-power-bi-content-pack-how-to/16.png) 

Jakmile bude nová verze balíčku obsahu funkční, můžete v případě potřeby odebrat starou verzi odstraněním podkladových sestav a databází přidružených danému balíčku obsahu.

## <a name="still-having-issues"></a>Pořád máte problémy? 

Podívejte se na [průvodce odstraňováním potíží](active-directory-reporting-troubleshoot-content-pack.md). Obecnou nápovědu k Power BI najdete v těchto [článcích nápovědy](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-started/).
 

## <a name="next-steps"></a>Další postup

Přehled generování sestav najdete v tématu [Generování sestav v Azure Active Directory](active-directory-reporting-azure-portal.md).
