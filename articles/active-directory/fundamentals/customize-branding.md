---
title: Postup přidání brandingu na přihlašovací stránku služby Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak přidat branding vaší organizace na přihlašovací stránku služby Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 6a817d4e52d602c921cb04f8c40dbad632d41932
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731321"
---
# <a name="how-to-add-branding-to-your-azure-ad-sign-in-page"></a>Postupy: Přidání brandingu na přihlašovací stránku Azure AD
Pomocí loga vaší organizace a vlastní barevná schémata poskytují konzistentní vzhled a představu o přihlašovacích stránek služby Azure Active Directory (Azure AD). Vaše přihlašovací stránky se zobrazí při přihlášení uživatele k vaší organizaci webové aplikace, jako je Office 365, který používá Azure AD jako zprostředkovatele identity.

>[!Note]
>Přidání vlastní značka vyžaduje použití Azure Active Directory Premium 1, Premium 2 nebo edice Basic nebo máte licenci Office 365. Další informace o licencování a edicích, naleznete v tématu [registrace Azure AD Premium](active-directory-get-started-premium.md).<br><br>Edice Premium a Basic služby Azure AD jsou zákazníkům v Číně dostupné prostřednictvím celosvětové instance služby Azure Active Directory. Ve službě Azure provozovaný společností 21Vianet v Číně nejsou aktuálně podporovány edice Azure AD Premium a Basic. Další informace, kontaktujte nás pomocí [fóru služby Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Přizpůsobit přihlašovací stránku Azure AD
Můžete přizpůsobit vaší služby Azure AD přihlašovacích stránek služby, které se zobrazí, když se uživatelé přihlásí do aplikace specifickým pro tenanta vaší organizace, jako například [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com), nebo při předání proměnné domény, jako je například [ *https://passwordreset.microsoftonline.com/?whr=contoso.com*](https://passwordreset.microsoftonline.com/?whr=contoso.com).

Vlastní značka se okamžitě nezobrazí vaši uživatelé, kteří přejdou do lokalit, třeba www.office.com. Místo toho uživatel musí přihlásit před přizpůsobený branding se zobrazí.

> [!NOTE]
> Všechny značky elementů jsou volitelné. Například pokud zadáte banner s logem se žádný obrázek na pozadí, přihlašovací stránka zobrazí vaše logo s výchozí obrázek na pozadí z cílové lokality (třeba Office 365).<br><br>Kromě toho branding přihlašovací stránky se nepřenáší na osobní účty Microsoft. Pokud uživatelé nebo obchodní hosté přihlásí pomocí osobního účtu Microsoft, přihlašovací stránka nebude se Branding vaší organizace.

### <a name="to-customize-your-branding"></a>Přizpůsobení brandingu
1. Přihlaste se k [portálu Azure AD](https://portal.azure.com/) pomocí účtu globálního správce adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **značky společnosti**a pak vyberte **konfigurovat**.

    ![Contoso – okno značky společnosti, zvýrazněnou možností konfigurovat](media/customize-branding/company-branding-configure-button.png)

3. Na **konfigurovat vlastní firemní branding** okně zadejte některé nebo všechny následující informace:

    - **Obecné nastavení**

        ![Konfigurovat branding okně dokončení obecné nastavení společnosti](media/customize-branding/configure-company-branding-general-settings.png)

        - **Jazyk.** Jazyk se automaticky nastaví jako výchozí a nedá se změnit.
        
        - **Obrázek pozadí přihlašovací stránky.** Vyberte soubor obrázku ve formátu PNG nebo JPG jako pozadí přihlašovací stránky. Na obrázku nemůže být větší než 1920 × 1080 pixelů, velikosti a musí mít velikost souboru menší než 300 kB.

        - **Úvodní nápis logo.** Vyberte verzi .png nebo .jpg logo se zobrazí na přihlašovací stránce poté, co uživatel zadá uživatelské jméno a na **Moje aplikace** stránky portálu. Na obrázku nemůže být vyšší než 36 pixelů nebo větší než 245 pixelů. Doporučujeme použít průhledný obrázek, protože na pozadí se nemusí shodovat pozadí loga. Doporučujeme také Nepřidání výplně kolem obrázku nebo může mít logo vypadat malé.

        - **Pomocný parametr uživatelského jména.** Zadejte text nápovědy, který se uživatelům zobrazí, pokud zapomenou své uživatelské jméno. Tento text musí být v kódování Unicode, bez odkazů nebo kód a nemůže být delší než 64 znaků. Pokud hosté přihlásí do vaší aplikace, doporučujeme Nepřidání Tento pomocný parametr.

        - **Text přihlašovací stránky.** Zadejte text, který se zobrazí ve spodní části přihlašovací stránky. Tento text můžete sdělit Další informace, jako je třeba telefonní číslo pro vaše oddělení technické podpory nebo právní prohlášení. Tento text musí být v kódování Unicode a není delší než 256 znaků. Doporučujeme také nezahrnuje odkazy nebo značky jazyka HTML.

    - **Upřesňující nastavení**
            
        ![Konfigurace vlastní firemní branding okně s rozšířeným nastavením dokončeno](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Barva pozadí přihlašovací stránky.** Určení barvy v šestnáctkovém formátu (bílé je třeba #FFFFFF), který se zobrazí místo image na pozadí v situacích, připojení s malou šířkou pásma. Doporučujeme použít primární barvu vaše banner s logem nebo barvy vaší organizace.

        - **Obrázek čtvercového loga.** Vyberte ve formátu PNG (preferované) nebo JPG obrázek loga vaší organizace se zobrazí uživatelům během procesu instalace pro nové zařízení s Windows 10 Enterprise. Tato image se používá jenom pro ověřování Windows a zobrazí se pouze na klienty, kteří používají [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) pro nasazení nebo pro zadávání hesel stránky v jiných Windows 10 dojde. Na obrázku nemůže být větší než velikost 240 × 240 pixelů a musí mít velikost souboru menší než 10 kB. Doporučujeme použít průhledný obrázek, protože na pozadí se nemusí shodovat pozadí loga. Doporučujeme také Nepřidání výplně kolem obrázku nebo může mít logo vypadat malé.
    
        - **Obrázek čtvercového loga, tmavé.** Stejné jako na obrázku čtvercového loga výše. Tento obrázek loga zaujímá místo obrázku čtvercového loga při použití s tmavého pozadí, například s obrazovkami připojený k Windows 10 Azure AD během prostředí out-of-box (OOBE).  Pokud vaše logo vypadá dobře na bílý tmavě modrá a černé pozadí, není nutné přidat tento obrázek. 
        
        - **Zobrazit možnost zachovat přihlášení.** Můžete uživatelům zůstane přihlášený do služby Azure AD, dokud explicitně odhlášení. Pokud se rozhodnete **ne**, je tato volba skrytá a uživatelé musí přihlásit pokaždé, když je prohlížeč zavřít a znovu otevřít.
        
            >[!Note]
            >Některé funkce služeb SharePoint Online a Office 2010 závisí na tom, jestli mají uživatelé možnost zůstat přihlášení. Pokud nastavíte **Ne**, můžou se vašim uživatelům zobrazovat další neočekávané výzvy k přihlášení.
   

3. Po dokončení přidáním brandingu, vyberte **Uložit**.

  Protože je to vaše první nastavení brandingu, stane se výchozí hodnota pro vašeho tenanta. Pokud chcete přidat další konfigurace, získáte možnost si vybrat výchozí.

  >[!Important]
  >Chcete-li přidat více firemní branding konfigurace k vašemu tenantovi, musíte zvolit **nový jazyk** na **Contoso - firemní branding** okno. Tím se otevře **konfigurovat vlastní firemní branding** okno, kde můžete postupovat podle stejných kroků jako výše.

## <a name="update-your-custom-branding"></a>Aktualizovat vlastní značky
Po vytvoření vlastní značky, můžete přejít zpět a změňte všechno, co chcete.

### <a name="to-edit-your-custom-branding"></a>Chcete-li upravit vlastní značky
1. Přihlaste se k [portálu Azure AD](https://portal.azure.com/) pomocí účtu globálního správce adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **značky společnosti**a pak vyberte **konfigurovat**.

    ![Contoso – okno značky společnosti, s výchozí konfigurací ukázka](media/customize-branding/company-branding-default-config.png)

3. Na **konfigurovat vlastní firemní branding** přidat, odebrat nebo změnit libovolné informace, na základě popisů v okně [přizpůsobit přihlašovací stránku Azure AD](#customize-your-azure-ad-sign-in-page) části tohoto článku.

4. Vyberte **Uložit**.

  Změny brandingu přihlašovací stránky se můžou projevit třeba až za hodinu.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Přidání firemního brandingu pro konkrétní jazyk do adresáře
Původní konfiguraci jazyka nelze změnit z výchozí jazyk. Pokud potřebujete konfigurace v jiném jazyce, ale můžete vytvořit novou konfiguraci.

### <a name="to-add-a-language-specific-branding-configuration"></a>Chcete-li přidat nastavení brandingu specifické pro jazyk

1. Přihlaste se k [portálu Azure AD](https://portal.azure.com/) pomocí účtu globálního správce adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **značky společnosti**a pak vyberte **nový jazyk**.

    ![Contoso – okno značky společnosti, se zvýrazněnou možností nový jazyk](media/customize-branding/company-branding-new-language.png)

3. Na **konfigurovat vlastní firemní branding** okno, vyberte svůj jazyk (například francouzština) a pak přidejte přeložené údajů na základě popisů v [přizpůsobit přihlašovací stránku Azure AD](#customize-your-azure-ad-sign-in-page) část tohoto článku.

4. Vyberte **Uložit**.

    **Contoso – vlastní firemní branding** okno se aktualizuje a zobrazí nové francouzské konfigurace.

    ![Contoso – okno značky společnosti, s výchozí konfigurací ukázka](media/customize-branding/company-branding-french-config.png)