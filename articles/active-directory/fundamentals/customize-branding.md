---
title: Přizpůsobení přihlašovací stránky tenanta Azure AD | Microsoft Docs
description: Zjistěte, jak na přihlašovací stránku Azure přidat firemní branding
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 07/20/2018
ms.author: lizross
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 7804d6b0d4a100997fb545e678458424dac6ceed
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227258"
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Rychlý start: Přidání firemního brandingu na přihlašovací stránku služby Azure AD
Mnoho společností chce předcházet zmatení uživatele a upřednostňuje jednotný vzhled všech webů a služeb, které spravují. Azure Active Directory (Azure AD) nabízí možnost přizpůsobení vzhledu přihlašovací stránky pomocí loga společnosti a vlastních barevných témat. Přihlašovací stránka se zobrazuje při přihlašování k webovým aplikacím jako Office 365, které používají Azure AD jako poskytovatele identit. Na této stránce zadáváte své přihlašovací údaje.

> [!NOTE]
> * Firemní branding je dostupný jenom v případě, že si zakoupíte licenci Azure AD Premium nebo Basic nebo máte licenci Office 365. Informace o tom, jestli je určitá funkce podporovaná vaším typem licence, najdete na stránce [s informacemi o cenách Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Edice Premium a Basic služby Azure AD jsou zákazníkům v Číně dostupné prostřednictvím celosvětové instance služby Azure Active Directory. Edice Premium a Basic služby Azure AD nejsou aktuálně podporované ve službě Azure provozované v Číně společností 21Vianet. Další informace si můžete vyžádat na [fóru služby Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Přizpůsobení přihlašovací stránky

<!--You can customize the following elements on the sign-in page: <attach image>-->

Přizpůsobení firemního brandingu se zobrazí na přihlašovací stránce Azure AD, když chtějí uživatelé získat přístup k adrese URL určitého tenanta, třeba [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com), nebo při předávání proměnné domény v adrese URL, třeba [*https://passwordreset.microsoftonline.com/?whr=contoso.com*](https://passwordreset.microsoftonline.com/?whr=contoso.com)

Například když uživatelé navštíví web www.office.com, na přihlašovací stránce se nezobrazí žádný přizpůsobený firemní branding, protože uživatel ještě nezadal přihlašovací údaje. Jakmile uživatel zadá ID uživatele nebo vybere dlaždici uživatele, zobrazí se firemní branding.

> [!NOTE]
> * Název domény musí být v části **Domény** na webu Azure Portal, kde jste nakonfigurovali branding, uvedený jako „Aktivní“. Další informace najdete v článku [Přidání vlastního názvu domény](add-custom-domain.md).
> * Branding přihlašovací stránky se nepřenáší na přihlašovací stránku pro osobní účty Microsoftu. Pokud se zaměstnanci nebo obchodní hosté přihlásí pomocí osobního účtu Microsoft, na jejich přihlašovací stránce se branding vaší organizace nezobrazí.


### <a name="banner-logo"></a>Banner s logem 

Popis | Omezení | Doporučení
------- | ------- | ----------
Banner s logem se zobrazuje na přihlašovací stránce a na stránce přístupového panelu.<br>Na přihlašovací stránce se logo zobrazí po zadání uživatelského jména. | Transparentní JPG nebo PNG<br>Maximální výška: 36 pixelů<br>Maximální šířka: 245 pixelů | Tady použijte logo své organizace.<br>Použijte transparentní obrázek. Nepředpokládejte, že bude pozadí bílé.<br>V obrázku nepřidávejte kolem loga odsazení, jinak bude logo vypadat nepřiměřeně malé.

### <a name="username-hint"></a>Nápověda uživatelského jména   
Popis | Omezení | Doporučení
------- | ------- | ----------
Tato možnost slouží k přizpůsobení textu nápovědy v poli uživatelského jména. | Text v kódu Unicode, až 64 znaků<br>Jenom prostý text | Pokud očekáváte, že se budou k vaší aplikaci přihlašovat i uživatelé mimo vaši organizaci, doporučujeme tuto možnost nenastavovat.
            
### <a name="sign-in-page-text"></a>Text na přihlašovací stránce   
Popis | Omezení | Doporučení
------- | ------- | ----------
Tato možnost se zobrazí ve spodní části přihlašovacího formuláře a slouží ke sdělování dalších informací, jako je telefonní číslo na podporu nebo právní prohlášení. | Text v kódu Unicode, až 256 znaků<br>Jenom prostý text (žádné odkazy nebo značky jazyka HTML).    

### <a name="sign-in-page-image"></a>Obrázek na přihlašovací stránce  
Popis | Omezení | Doporučení
------- | ------- | ----------
Tato možnost se zobrazuje na pozadí přihlašovací stránky, je ukotvená ke středu zobrazitelného místa a její velikost se zvětší nebo ořízne tak, aby vyplnila okno prohlížeče.    <br>Na úzkých obrazovkách, třeba na telefonech, se tento obrázek nezobrazuje.<br>Při načítání stránky je tento obrázek překrytý černou maskou s krytím 0,55. | JPG nebo PNG<br>Rozměry obrázku: 1920 × 1080 pixelů<br>Velikost souboru: &lt; 300 kB | <br>Obrázky použijte tam, kde není potřeba se silně soustředit na předmět. Ve středu tohoto obrázku se zobrazí neprůhledný přihlašovací formulář, který může v závislosti na velikosti okna prohlížeče zakrýt kteroukoli část obrázku.<br>Soubor by měl být malý, aby se zaručilo rychlé načítání. 

### <a name="sign-in-page-background-color"></a>Barva pozadí na přihlašovací stránce
Popis | Omezení | Doporučení
------- | ------- | ----------
V případě připojení s nízkou šířkou pásma tato plná barva nahrazuje obrázek na pozadí. | Barva RGB v šestnáctkovém formátu (příklad: #FFFFFF). | Doporučujeme použít primární barvu banneru s logem nebo barvu vaší organizace.

### <a name="square-logo-image"></a>Čtvercový obrázek loga
Popis | Omezení | Doporučení
------- | ------- | ----------
Tento obrázek se zobrazí při instalaci nových počítačů s Windows 10 Enterprise. Poskytuje kontext zaměstnancům, kteří nastavují svůj nový pracovní počítač. Obrázek se zobrazí u tenantů, kteří nasazují pracovní zařízení pomocí řešení [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97), a na stránkách pro zadání hesla v jiných prostředích Windows 10. | Transparentní PNG (upřednostňované) nebo JPG<br>Rozměry obrázku: 240 × 240 pixelů<br>Velikost souboru: &lt; 10 kB | Tady použijte logo své organizace.<br> Použijte transparentní obrázek.<br>Nepředpokládejte, že bude pozadí bílé.<br>V obrázku nepřidávejte kolem loga odsazení, jinak bude logo vypadat nepřiměřeně malé.

### <a name="show-option-to-remain-signed-in"></a>Zobrazit možnost zachovat přihlášení
Popis | Omezení | Doporučení
------- | ------- | ----------
Při přihlašování k Azure AD má uživatel možnost zůstat přihlášený i po zavření a opětovném otevření prohlížeče. Toto nastavení tuto možnost skryje.<br>Pokud chcete, aby uživatelé tuto možnost neviděli, nastavte **Ne**. | &nbsp; | Skrytí této možnosti nemá vliv na životnost relace.<br>Některé funkce služeb SharePoint Online a Office 2010 závisí na tom, jestli mají uživatelé možnost zůstat přihlášení. Pokud nastavíte **Ne**, můžou se vašim uživatelům zobrazovat další neočekávané výzvy k přihlášení.

> [!NOTE]
> Všechny prvky jsou volitelné. Pokud třeba použijete banner s logem bez obrázku na pozadí, na přihlašovací stránce se zobrazí vaše logo a obrázek na pozadí pro cílový web (třeba Office 365).

## <a name="add-company-branding-to-your-directory"></a>Přidat firemního brandingu do vašeho adresáře

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má k tenantovi oprávnění globálního správce.
2. Vyberte **Azure Active Directory** > **Branding společnosti** > **Upravit**.
  
  ![Otevírání vlastního brandingu](./media/customize-branding/navigation-to-branding.png)
3. Upravte prvky, které chcete přizpůsobit. Všechny prvky jsou volitelné.
  
  ![Úprava vlastního brandingu](./media/customize-branding/edit-branding.png)
4. Jakmile budete mít hotovo, vyberte **Uložit**.

Změny brandingu přihlašovací stránky se můžou projevit třeba až za hodinu.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Přidání firemního brandingu pro konkrétní jazyk do adresáře

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má k adresáři oprávnění globálního správce.
2. Vyberte **Azure Active Directory** > **Branding společnosti** > **Nový jazyk**.
  
  ![Přidání elementů brandingu pro konkrétní jazyk](./media/customize-branding/add-language.png)
3. Upravte prvky, které chcete přizpůsobit. Všechny prvky jsou volitelné.
4. Jakmile budete mít hotovo, vyberte **Uložit**.

Změny brandingu přihlašovací stránky se můžou projevit třeba až za hodinu.

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste se dozvěděli, jak přidat do adresáře služby Azure AD branding společnosti. 

Ke konfiguraci brandingu společnosti v Azure AD pomocí webu Azure Portal můžete použít následující odkaz.

> [!div class="nextstepaction"]
> [Konfigurace značky společnosti](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 