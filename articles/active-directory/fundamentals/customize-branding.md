---
title: Přidání brandingu na přihlašovací stránku vaší organizace – Azure AD
description: Pokyny, jak přidat branding vaší organizace na přihlašovací stránku Azure Active Directory
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/24/2020
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04e1d35ab17a49dd1c4e9bd2bd19289de2b8658a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565850"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Přidání brandingu na přihlašovací stránku Azure Active Directory vaší organizace
Použijte logo vaší organizace a vlastní barevná schémata k zajištění konzistentního vzhledu na přihlašovacích stránkách Azure Active Directory (Azure AD). Přihlašovací stránky se zobrazí, když se uživatelé přihlásí k webovým aplikacím vaší organizace, jako je například Microsoft 365, který jako poskytovatele identity používá službu Azure AD.

>[!NOTE]
>Přidání vlastního brandingu vyžaduje, abyste použili edice Azure Active Directory Premium 1, Premium 2 nebo Basic nebo aby Microsoft 365 licenci. Další informace o licencování a edicích najdete v tématu [Registrace pro Azure AD Premium](active-directory-get-started-premium.md).<br><br>Edice Premium a Basic služby Azure AD jsou zákazníkům v Číně dostupné prostřednictvím celosvětové instance služby Azure Active Directory. Edice Premium a Basic služby Azure AD v současnosti nejsou podporované ve službě Azure provozované v Číně společností 21Vianet. Můžete se na nás obrátit na [fóru služby Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/), kde se dozvíte další informace.

## <a name="customize-your-azure-ad-sign-in-page"></a>Přizpůsobení přihlašovací stránky Azure AD
Můžete přizpůsobit přihlašovací stránky služby Azure AD, které se zobrazí, když se uživatelé přihlásí k aplikacím specifickým pro tenanta, jako `https://outlook.com/contoso.com` je například nebo při předávání proměnné domény, například `https://passwordreset.microsoftonline.com/?whr=contoso.com` .

Vaše vlastní branding se okamžitě nezobrazí, když uživatelé přejdou na weby, jako je například webová \. Office.com. Místo toho se uživatel musí přihlásit předtím, než se zobrazí vaše přizpůsobené branding. Až se uživatel přihlásí, branding může trvat 15 minut nebo déle. 

> [!NOTE]
> Všechny prvky brandingu jsou volitelné. Pokud například zadáte logo banneru bez obrázku na pozadí, přihlašovací stránka zobrazí vaše logo s výchozí imagí pozadí z cílové lokality (například Microsoft 365).<br><br>Značky přihlašovací stránky se navíc nepřenášejí na osobní účty Microsoft. Pokud se vaši uživatelé nebo obchodní hosté přihlásí pomocí osobního účet Microsoft, přihlašovací stránka neodráží branding vaší organizace.

### <a name="to-customize-your-branding"></a>Přizpůsobení brandingu
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **Branding společnosti**a pak vyberte **Konfigurovat**.

    ![Contoso – stránka brandingu společnosti, konfigurace zvýrazněné možnosti](media/customize-branding/company-branding-configure-button.png)

3. Na stránce **Konfigurovat Branding společnosti** zadejte všechny nebo všechny následující informace.

    >[!IMPORTANT]
    >Všechny vlastní obrázky, které na této stránce přidáte, mají velikost obrazu (v pixelech) a potenciálně velikost souboru (KB), omezení. Z důvodu těchto omezení bude pravděpodobně nutné použít Editor fotek k vytvoření obrázků ve správné velikosti.

    - **Obecná nastavení**

        ![Stránka Konfigurace brandingu společnosti s dokončeným obecným nastavením](media/customize-branding/configure-company-branding-general-settings.png)

        - **Language.** Jazyk se automaticky nastaví jako výchozí a nedá se změnit.
        
        - **Obrázek pozadí přihlašovací stránky** Vyberte soubor obrázku. png nebo. jpg, který se zobrazí jako pozadí pro přihlašovací stránky. Obrázek bude ukotven do středu prohlížeče a bude se škálovat na velikost zobrazitelného prostoru. Nemůžete vybrat obrázek, který je větší než velikost 1080 pixelů nebo má velikost souboru větší než 300 KB.
        
            Doporučuje se používat obrázky bez fokusu se silným předmětem, např. neprůhledné prázdné pole se zobrazuje uprostřed obrazovky a může krýt jakoukoli část obrázku v závislosti na rozměrech zobrazitelného prostoru.

        - **Logo banner.** Po zadání uživatelského jména a na stránce portálu **Moje aplikace** vyberte verzi vašeho loga. png nebo. jpg, která se zobrazí na přihlašovací stránce.
            
            Obrázek nemůže být větší než 60 pixelů nebo širší než 280 pixelů. Doporučujeme použít transparentní obrázek, protože pozadí se nemusí shodovat s logem na pozadí. Doporučujeme také Nepřidávat odsazení kolem obrázku, jinak může dojít k tomu, že vaše logo bude mít malý vzhled.

        - **Nápověda k uživatelskému jménu** Zadejte text nápovědy, který se zobrazí uživatelům, pokud zapomene své uživatelské jméno. Tento text musí být v kódování Unicode, bez odkazů nebo kódu a nesmí překročit 64 znaků. Pokud se hosté přihlásí do vaší aplikace, Doporučujeme nepřidávat Tento pomocný parametr.

        - **Text a formátování přihlašovací stránky** Zadejte text, který se zobrazí v dolní části přihlašovací stránky. Tento text můžete použít k sdělování dalších informací, jako je telefonní číslo na oddělení technické podpory nebo právní prohlášení. Tento text musí být Unicode a nesmí přesáhnout 1024 znaků.

           Můžete přizpůsobit text přihlašovací stránky, kterou jste zadali. Chcete-li začít nový odstavec, použijte dvakrát klávesu ENTER. Formátování textu můžete také změnit tak, aby obsahovalo tučné písmo, kurzívu, podtržení nebo odkaz na odkaz. K přidání formátování textu použijte následující syntax: 

          > Cíl ```[text](link)``` 
          
          > Tučné písmo: ``` **text** ``` nebo ``` __text__ ``` 
          
          > Kurzíva: ``` *text* ``` nebo ``` _text_ ``` 
          
          > Podtržení ``` ++text++ ``` 

    - **Rozšířená nastavení**
            
        ![Stránka Konfigurace brandingu společnosti s dokončenými pokročilými nastaveními](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Barva pozadí přihlašovací stránky** Zadejte hexadecimální barvu (například bílá je #FFFFFF), která se zobrazí na místě obrázku pozadí v situacích připojení s malou šířkou pásma. Doporučujeme použít primární barvu loga banneru nebo barvy vaší organizace.

        - **Obrázek čtvercového loga** Vyberte obrázek s příponou. png (upřednostňovaný) nebo. jpg, který se zobrazí uživatelům během procesu instalace pro nová podniková zařízení s Windows 10. Tato image se používá jenom pro ověřování systému Windows a zobrazuje se jenom na klientech, kteří používají [Windows autopilot]( /windows/deployment/windows-autopilot/windows-10-autopilot) pro nasazení, nebo pro zadávání hesel v jiných prostředích Windows 10. V některých případech se může zobrazit také v dialogovém okně pro vyjádření souhlasu.
        
            Velikost obrázku nemůže být větší než 240x240 pixelů a velikost souboru musí být menší než 10 KB. Doporučujeme použít transparentní obrázek, protože pozadí se nemusí shodovat s logem na pozadí. Doporučujeme také Nepřidávat odsazení kolem obrázku, jinak může dojít k tomu, že vaše logo bude mít malý vzhled.
    
        - **Obrázek čtvercového loga, tmavý motiv** Stejné jako u obrázku čtvercového loga výše. Tento obrázek loga při použití s tmavým pozadím získá místo obrázku čtvercového loga, jako jsou obrazovky připojené ke službě Azure AD s Windows 10 během spouštěného spouštěného prostředí (OOBE).  Pokud vaše logo vypadá dobře na bílém, tmavém a černém pozadí, nemusíte tento obrázek přidávat. 
        
        - **Zobrazit možnost zůstat přihlášeni.** Můžete zvolit, aby vaši uživatelé měli zůstat přihlášení ke službě Azure AD, dokud se explicitně odhlásíte. Pokud zvolíte **ne**, tato možnost bude skrytá a uživatelé se musí přihlašovat pokaždé, když se prohlížeč zavře a znovu otevře.

            Tato funkce je k dispozici pouze na výchozím objektu brandingu a nikoli na objektu specifickém pro daný jazyk. Další informace o konfiguraci a řešení potíží s možnostmi zůstat přihlášené najdete v tématu [Konfigurace "zůstat přihlášeného?" pro účty Azure AD](keep-me-signed-in.md) .
        
            >[!NOTE]
            >Některé funkce služeb SharePoint Online a Office 2010 závisí na tom, jestli mají uživatelé možnost zůstat přihlášení. Pokud nastavíte **Ne**, můžou se vašim uživatelům zobrazovat další neočekávané výzvy k přihlášení.
   

3. Až dokončíte přidávání brandingu, vyberte **Uložit**.

    Pokud tento proces vytvoří první vlastní konfiguraci brandingu, bude pro vašeho tenanta výchozí. Pokud máte další konfigurace, budete si moct vybrat výchozí konfiguraci.
    
    >[!IMPORTANT]
    >Pokud chcete do tenanta přidat další konfigurace firemního brandingu, musíte zvolit **Nový jazyk** na stránce **výrobce společnosti Contoso** . Tím se otevře stránka **Konfigurovat Branding společnosti** , kde můžete postupovat podle výše uvedených kroků.

## <a name="update-your-custom-branding"></a>Aktualizace vlastního brandingu
Po vytvoření vlastního brandingu se můžete vrátit zpátky a změnit všechno, co potřebujete.

### <a name="to-edit-your-custom-branding"></a>Úprava vlastního brandingu
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **Branding společnosti**a pak vyberte **Konfigurovat**.

    ![Contoso – stránka brandingu společnosti s zobrazenou výchozí konfigurací](media/customize-branding/company-branding-default-config.png)

3. Na stránce **Konfigurovat Branding společnosti** přidejte, odeberte nebo změňte jakékoli informace na základě popisu v části [přizpůsobení přihlašovací stránky Azure AD](#customize-your-azure-ad-sign-in-page) tohoto článku.

4. Vyberte **Uložit**.

   Změny brandingu přihlašovací stránky se můžou projevit třeba až za hodinu.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Přidání firemního brandingu pro konkrétní jazyk do adresáře
Jazyk původní konfigurace nemůžete změnit z výchozího jazyka. Pokud ale potřebujete konfiguraci v jiném jazyce, můžete vytvořit novou konfiguraci.

### <a name="to-add-a-language-specific-branding-configuration"></a>Přidání konfigurace brandingu konkrétního jazyka

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **Branding společnosti**a pak vyberte **Nový jazyk**.

    ![Contoso – stránka brandingu společnosti s zvýrazněnou možností nový jazyk](media/customize-branding/company-branding-new-language.png)

3. Na stránce **Konfigurovat Branding společnosti** vyberte svůj jazyk (například francouzštinu) a pak přidejte přeložené informace na základě popisu v části [vlastní nastavení přihlašovací stránky Azure AD](#customize-your-azure-ad-sign-in-page) tohoto článku.

4. Vyberte **Uložit**.

    Společnost **Contoso – značka** stránky se aktualizuje, aby se zobrazila vaše nová Francouzská konfigurace.

    ![Contoso – stránka brandingu společnosti s uvedenou novou jazykovou konfigurací](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Přidání vlastního brandingu na stránky
Přidejte vlastní branding na stránky úpravou konce adresy URL textem, `?whr=yourdomainname` . Tato úprava funguje na několika stránkách, včetně stránky nastavení Multi-Factor Authentication (MFA), stránky nastavení samoobslužného resetování hesla (SSPR) a přihlašovací stránky.

**Příklady:**

**Původní adresa URL:**https://aka.ms/MFASetup<br>
**Vlastní adresa URL:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**Původní adresa URL:**https://aka.ms/SSPR<br>
**Vlastní adresa URL:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`