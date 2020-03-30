---
title: Přidání značky na přihlašovací stránku vaší organizace – Azure AD
description: Pokyny, jak přidat značku vaší organizace na přihlašovací stránku služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441fdd14cc2c734b6ce532f3ad1d30663b2f56c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049798"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Přidání značky na přihlašovací stránku služby Azure Active Directory vaší organizace
Pomocí loga vaší organizace a vlastních barevných schémat můžete na přihlašovacích stránkách služby Azure Active Directory (Azure AD) zajistit konzistentní vzhled a chování. Vaše přihlašovací stránky se zobrazí, když se uživatelé přihlásí k webovým aplikacím vaší organizace, jako je Office 365, který používá Azure AD jako poskytovatele identity.

>[!Note]
>Přidání vlastní značky vyžaduje použití edic Azure Active Directory Premium 1, Premium 2 nebo Basic nebo licenci Office 365. Další informace o licencování a edicích najdete v tématu [Registrace k Azure AD Premium](active-directory-get-started-premium.md).<br><br>Edice Premium a Basic služby Azure AD jsou zákazníkům v Číně dostupné prostřednictvím celosvětové instance služby Azure Active Directory. Edice Premium a Basic služby Azure AD v současnosti nejsou podporované ve službě Azure provozované v Číně společností 21Vianet. Můžete se na nás obrátit na [fóru služby Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/), kde se dozvíte další informace.

## <a name="customize-your-azure-ad-sign-in-page"></a>Přizpůsobení přihlašovací stránky Azure AD
Přihlašovací stránky Azure AD, které se zobrazí, když se uživatelé přihlásí k aplikacím `https://outlook.com/contoso.com`vaší organizaci specifickým pro `https://passwordreset.microsoftonline.com/?whr=contoso.com`klienta, jako je například aplikace nebo když předávají proměnnou domény, například .

Vaše vlastní značka se okamžitě nezobrazí, když uživatelé\.přejdou na weby, jako je www office.com. Místo toho se uživatel musí přihlásit, než se zobrazí přizpůsobená značka. Po přihlášení uživatele může zobrazení značky trvat 15 minut nebo déle. 

> [!NOTE]
> Všechny prvky značky jsou volitelné. Pokud například zadáte logo banneru bez obrázku pozadí, zobrazí se na přihlašovací stránce vaše logo s výchozím obrázkem pozadí z cílového webu (například Office 365).<br><br>Značka přihlašovací stránky se navíc nepřenáší na osobní účty Microsoft. Pokud se uživatelé nebo firemní hosté přihlašují pomocí osobního účtu Microsoft, přihlašovací stránka nebude odrážet značku vaší organizace.

### <a name="to-customize-your-branding"></a>Přizpůsobení značky
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte Značka **společnosti**a pak vyberte **Konfigurovat**.

    ![Stránka Značky Contoso – Firemní značka, Zvýrazněná možnost Konfigurovat](media/customize-branding/company-branding-configure-button.png)

3. Na stránce **Konfigurovat značku společnosti** zadejte některé nebo všechny následující informace.

    >[!Important]
    >Všechny vlastní obrázky, které přidáte na tuto stránku, mají velikost obrázku (pixely) a potenciálně velikost souboru (KB), omezení. Z důvodu těchto omezení budete s největší pravděpodobností muset použít editor fotografií k vytvoření obrázků správné velikosti.

    - **Obecná nastavení**

        ![Konfigurace stránky značky společnosti s dokončením obecných nastavení](media/customize-branding/configure-company-branding-general-settings.png)

        - **Jazyk.** Jazyk je automaticky nastaven jako výchozí a nelze jej změnit.
        
        - **Obrázek pozadí přihlašovací stránky** Vyberte soubor png nebo JPG, který se zobrazí jako pozadí vašich přihlašovacích stránek. 
        
            Obrázek nesmí být větší než 1920x1080 pixelů velikosti a musí mít velikost souboru menší než 300 kB.

        - **Logo banneru.** Vyberte verzi loga PNG nebo JPG, která se zobrazí na přihlašovací stránce poté, co uživatel zadá uživatelské jméno a na stránce Portál **mých aplikací.**
            
            Obraz nemůže být vyšší než 60 pixelů nebo širší než 280 pixelů. Doporučujeme použít průhledný obrázek, protože pozadí nemusí odpovídat pozadí loga. Doporučujeme také nepřidávat polstrování kolem obrázku nebo by mohlo vaše logo vypadat malé.

        - **Nápověda k uživatelskému jménu.** Zadejte text nápovědy, který se uživatelům zobrazí, pokud zapomenou své uživatelské jméno. Tento text musí být Unicode, bez odkazů nebo kódu a nesmí překročit 64 znaků. Pokud se hosté přihlásí do vaší aplikace, doporučujeme tuto nápovědu nepřidávat.

        - **Text přihlašovací stránky.** Zadejte text, který se zobrazí v dolní části přihlašovací stránky. Tento text můžete použít ke sdělení dalších informací, jako je telefonní číslo na technickou podporu nebo právní prohlášení. Tento text musí být unicode a nesmí přesáhnout 256 znaků. Doporučujeme také nezačlenovat odkazy nebo značky HTML.

    - **Upřesnit nastavení**
            
        ![Konfigurace stránky značky společnosti s dokončeným rozšířeným nastavením](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Barva pozadí přihlašovací stránky** Zadejte šestnáctkovou barvu (například bílá je #FFFFFF), která se zobrazí místo obrázku na pozadí v situacích připojení s malou šířkou pásma. Doporučujeme použít primární barvu loga banneru nebo barvu vaší organizace.

        - **Obrázek čtvercového loga.** Vyberte obrázek PNG (upřednostňovaný) nebo .jpg loga vaší organizace, který se uživatelům zobrazí během procesu instalace nových zařízení s Windows 10 Enterprise. Tato bitová kopie se používá pouze pro ověřování systému Windows a zobrazuje se pouze u klientů, kteří používají [systém Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) pro nasazení nebo pro stránky pro zadávání hesel v jiných prostředích systému Windows 10. V některých případech se může také zobrazit v dialogovém okně souhlasu.
        
            Obrázek nesmí být větší než 240 x 240 pixelů a musí mít velikost souboru menší než 10 kB. Doporučujeme použít průhledný obrázek, protože pozadí nemusí odpovídat pozadí loga. Doporučujeme také nepřidávat polstrování kolem obrázku nebo by mohlo vaše logo vypadat malé.
    
        - **Čtvercový obrázek loga, tmavý motiv.** Stejné jako obrázek čtvercového loga výše. Tento obrázek loga nahrazuje obrázek čtvercového loga při použití s tmavým pozadím, například s obrazovkami s Windows 10 Azure AD připojované obrazovkami během prostředí out-of-box (OOBE).  Pokud vaše logo vypadá dobře na bílém, tmavě modrém a černém pozadí, nemusíte tento obrázek přidávat. 
        
        - **Zobrazit možnost zůstat přihlášeni.** Můžete se rozhodnout, že uživatelé zůstanou přihlášení k Azure AD, dokud se explicitně neodhlašujete. Pokud zvolíte **Ne**, bude tato možnost skrytá a uživatelé se musí přihlásit při každém zavření a opětovném otevření prohlížeče.
        
            >[!Note]
            >Některé funkce služeb SharePoint Online a Office 2010 závisí na tom, jestli mají uživatelé možnost zůstat přihlášení. Pokud nastavíte **Ne**, můžou se vašim uživatelům zobrazovat další neočekávané výzvy k přihlášení.
   

3. Po přidání značky vyberte **Uložit**.

    Pokud tento proces vytvoří první vlastní konfiguraci značky, stane se výchozím pro vašeho tenanta. Pokud máte další konfigurace, budete moci zvolit výchozí konfiguraci.
    
    >[!Important]
    >Chcete-li do tenanta přidat další konfigurace firemníznačky, musíte na stránce **značky Contoso – společnost** zvolit **nový jazyk.** Otevře se stránka **Konfigurace značky společnosti,** kde můžete postupovat stejným způsobem jako výše.

## <a name="update-your-custom-branding"></a>Aktualizace vlastní značky
Po vytvoření vlastní značky se můžete vrátit a změnit vše, co chcete.

### <a name="to-edit-your-custom-branding"></a>Úprava vlastní značky
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte Značka **společnosti**a pak vyberte **Konfigurovat**.

    ![Contoso - Stránka značky společnosti s výchozí konfigurací](media/customize-branding/company-branding-default-config.png)

3. Na stránce **Konfigurace značky společnosti** přidejte, odeberte nebo změňte některé informace na základě popisů v části [Přizpůsobit přihlašovací stránku Azure AD v](#customize-your-azure-ad-sign-in-page) tomto článku.

4. Vyberte **Uložit**.

   Změny brandingu přihlašovací stránky se můžou projevit třeba až za hodinu.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Přidání firemního brandingu pro konkrétní jazyk do adresáře
Jazyk původní konfigurace nelze změnit z výchozího jazyka. Pokud však potřebujete konfiguraci v jiném jazyce, můžete vytvořit novou konfiguraci.

### <a name="to-add-a-language-specific-branding-configuration"></a>Přidání konfigurace brandingu specifické pro daný jazyk

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte Značka **společnosti**a pak vyberte **Nový jazyk**.

    ![Stránka značky Contoso – Firemní značka se zvýrazněnou možností Nový jazyk](media/customize-branding/company-branding-new-language.png)

3. Na stránce **Konfigurace značky společnosti** vyberte jazyk (například francouzštinu) a přidejte přeložené informace na základě popisů v části [Přizpůsobit přihlašovací stránku Azure AD v](#customize-your-azure-ad-sign-in-page) tomto článku.

4. Vyberte **Uložit**.

    Aktualizace stránky **značky Contoso – Společnost,** která zobrazuje novou francouzskou konfiguraci.

    ![Contoso - Stránka značky společnosti s výchozí konfigurací](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Přidání vlastní značky na stránky
Přidejte vlastní značku na stránky úpravou konce adresy `?whr=yourdomainname`URL s textem . Tato změna funguje na několika stránkách, včetně stránky nastavení vícefaktorového ověřování (MFA), stránky nastavení samoobslužného resetování hesla (SSPR) a přihlašovací stránky.

**Příklady:**

**Původní adresa URL:**https://aka.ms/MFASetup<br>
**Vlastní adresa URL:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**Původní adresa URL:**https://aka.ms/SSPR<br>
**Vlastní adresa URL:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`

 
