---
title: Přizpůsobit přihlašovací stránku pro klienta služby Azure Active Directory | Microsoft Docs
description: Informace o postupu přidání firemního brandingu na stránky Azure přihlášení
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 05/23/2018
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 5573399b33da64df8b9bbf78a9583572cf148d7b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713843"
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Rychlé spuštění: Přidání firemního brandingu na přihlašovací stránku ve službě Azure AD
Mnoho společností chce předcházet zmatení uživatele a upřednostňuje jednotný vzhled všech webů a služeb, které spravují. Azure Active Directory (Azure AD) tuto možnost nabízí a umožňuje přizpůsobení vzhledu stránku přihlášení s svoje firemní logo a vlastní barevná schémata. Na přihlašovací stránce se zobrazí při přihlášení k aplikacím založené na webu, například Office 365, které používají Azure AD jako zprostředkovatele identity. Budete používat tuto stránku k zadání pověření.

> [!NOTE]
> * Firemní branding je k dispozici pouze v případě, že jste zakoupili Premium nebo Basic licenci pro Azure AD, nebo mít licenci pro Office 365. Další informace, pokud je funkce nepodporuje váš typ licence, zkontrolujte [stránce s cenami. informace o službě Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Edice Azure AD Premium a Basic jsou zákazníkům v Číně dostupné prostřednictvím celosvětové instance služby Azure Active Directory. Ve službě Azure provozované v Číně společností 21Vianet nejsou aktuálně podporovány edice Azure AD Premium a Basic. Další informace, kontaktujte nás na [fóru služby Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Přizpůsobení přihlašovací stránky

<!--You can customize the following elements on the sign-in page: <attach image>-->

Firemního brandingu přizpůsobení zobrazí na přihlašovací stránce služby Azure AD, když uživatelé přistupují k adresy URL konkrétního klienta, jako [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com).

Když uživatelé navštíví www.office.com, například na přihlašovací stránce nezobrazí žádné firemního brandingu přizpůsobení, protože uživatel nebyl ještě zadali přihlašovací údaje. Jakmile uživatel zadá své ID uživatele nebo vyberete dlaždici uživatele, zobrazí se firemního brandingu.

> [!NOTE]
> * Název domény musí zobrazovat jako "Aktivní" v **domén** část portálu Azure, ve kterém jste branding nakonfigurovali. Další informace najdete v tématu [přidání vlastního názvu domény](add-custom-domain.md).
> * Branding přihlašovací stránky se nepřenáší na přihlašovací stránku pro osobní účty Microsoftu. Pokud zaměstnancům nebo obchodní hosté, přihlaste se pomocí osobního účtu Microsoft, jejich přihlašovací stránce nemusí odpovídat branding vaší organizace.


### <a name="banner-logo"></a>Banner s logem 

Popis | Omezení | Doporučení
------- | ------- | ----------
Banner s logem se zobrazí na přihlášení a stránkách panelu.<br>Na stránce přihlášení se zobrazí logo po zadání uživatelského jména. | Transparentní JPG nebo PNG<br>Maximální výška: 36 px<br>Maximální šířka: 245 px | Tady použijte logo vaší organizace.<br>Použijte průhledný obrázek. Nepředpokládejte, že bude bílé pozadí.<br>Nepřidávejte výplně kolem vaše logo v bitové kopii nebo vaše logo bude vypadat nepřiměřeně malé.

### <a name="username-hint"></a>Pomocný parametr uživatelského jména   
Popis | Omezení | Doporučení
------- | ------- | ----------
Tato možnost přizpůsobuje pomocný parametr text do pole uživatelské jméno. | Text v kódu Unicode až 64 znaků.<br>Jenom prostý text | Pokud očekáváte uživatele typu Host mimo vaši organizaci k přihlášení do aplikace, doporučujeme vám, že není nastavíte tuto možnost.
            
### <a name="sign-in-page-text"></a>Text na přihlašovací stránce   
Popis | Omezení | Doporučení
------- | ------- | ----------
Tato možnost se zobrazí v dolní části formuláře přihlášení a slouží ke komunikaci Další informace, jako je telefonní číslo, které má vaše oddělení technické podpory nebo právní prohlášení. | Text v kódu Unicode až 256 znaků.<br>Jenom prostý text (žádné odkazy nebo značky jazyka HTML).    

### <a name="sign-in-page-image"></a>Obrázek přihlašovací stránky  
Popis | Omezení | Doporučení
------- | ------- | ----------
Tato možnost se zobrazí na pozadí na přihlašovací stránce, je ukotven k centru zobrazitelné místa a měřítek a kultury okna prohlížeče.    <br>Na úzkých obrazovkách například mobilních telefonů se nezobrazí tuto bitovou kopii.<br>Černá maska s 0.55 krytí je použitá v průběhu tuto bitovou kopii při načtení stránky. | JPG nebo PNG<br>Obrázek dimenze: 1920 × 1080 pixelů<br>Velikost souboru: &lt; 300 KB | <br>Používat Image místě, kde není silné subjektu fokus. Formulář neprůhledného přihlášení se zobrazí na střed tuto bitovou kopii a může zahrnovat všechny část obrázku, v závislosti na velikosti okna prohlížeče.<br>Zachovejte velikost souboru zajistit časů rychlé načítání. 

### <a name="sign-in-page-background-color"></a>Barva pozadí na přihlašovací stránce
Popis | Omezení | Doporučení
------- | ------- | ----------
Tato barva použijí se místo obrázek pozadí na připojení s malou šířkou pásma. | Barva RGB v šestnáctkové soustavě (Příklad: #FFFFFF | Doporučujeme pomocí primární barvu banneru s logem nebo barva vaší organizace.

### <a name="square-logo-image"></a>Obrázek loga odmocnina
Popis | Omezení | Doporučení
------- | ------- | ----------
Tuto bitovou kopii, zobrazí se během instalace nového počítače systému Windows 10 Enterprise. Poskytuje kontext pro zaměstnance, při instalaci nové pracovnímu počítači. Image se zobrazí u klientů, které používají [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) k nasazení svých pracovních zařízení a na zadávání hesel vyskytne stránky v jiných Windows 10. | Transparentní JPG nebo PNG (doporučeno)<br>Obrázek dimenze: 240 x 240 px<br>Velikost souboru: &lt; 10 KB | Tady použijte logo vaší organizace.<br> Použijte průhledný obrázek.<br>Nepředpokládejte, že bude bílé pozadí.<br>Vaše logo v bitové kopii nepřidáte odsazení nebo vaše logo bude vypadat nepřiměřeně malé.

### <a name="show-option-to-remain-signed-in"></a>Zobrazit možnost zachovat přihlášení
Popis | Omezení | Doporučení
------- | ------- | ----------
Azure AD přihlášení umožňuje uživateli, které můžete zůstat přihlášení po jejich zavřete a znovu otevřete svého prohlížeče. Toto nastavení skryje tuto možnost.<br>Nastavte na **ne** skrýt tuto možnost od uživatelů. | &nbsp; | Skrytí možnost nemá vliv na dobu platnosti relace.<br>Některé funkce služby SharePoint Online a Office 2010, závisí na možnosti výběru zůstat přihlášeného uživatele. Pokud nastavíte tuto možnost na **ne**, může se uživatelům zobrazí další a neočekávané výzev k přihlášení.

> [!NOTE]
> Všechny prvky jsou volitelné. Například pokud určíte banner s logem se žádný obrázek na pozadí, na přihlašovací stránce se zobrazí vaše logo a obrázek pozadí pro cílové lokalitě (například Office 365).

## <a name="add-company-branding-to-your-directory"></a>Přidání firemního brandingu do vašeho adresáře

1. Přihlaste se k [do centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globální správce pro tenanta.
2. Vyberte **Azure Active Directory** > **firemní branding** > **upravit**.
  
  ![Otevírání vlastní branding](./media/customize-branding/navigation-to-branding.png)
3. Upravte prvky, které chcete přizpůsobit. Všechny prvky jsou volitelné.
  
  ![Upravit vlastní branding](./media/customize-branding/edit-branding.png)
4. Až budete hotoví, vyberte **Uložit**.

Může trvat až jednu hodinu pro všechny změny, které jste udělali na přihlašovací stránku branding zobrazí.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Přidání konkrétní jazyk firemního brandingu do vašeho adresáře

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má k adresáři oprávnění globálního správce.
2. Vyberte **Azure Active Directory** > **firemní branding** > **nový jazyk**.
  
  ![Přidání brandingu elementy jazyka](./media/customize-branding/add-language.png)
3. Upravte prvky, které chcete přizpůsobit. Všechny prvky jsou volitelné.
4. Až budete hotoví, vyberte **Uložit**.

Může trvat až jednu hodinu pro všechny změny, které jste udělali na přihlašovací stránku branding zobrazí.

## <a name="next-steps"></a>Další postup
V tento rychlý start když jste se naučili postup přidání firemního brandingu na adresář Azure AD. 

Na následující odkaz můžete použít ke konfiguraci vašeho firemního brandingu ve službě Azure AD na portálu Azure.

> [!div class="nextstepaction"]
> [Konfigurace značky společnosti](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 