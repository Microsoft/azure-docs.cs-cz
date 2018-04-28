---
title: 'Azure Active Directory B2C: Nástroj pomocné rutiny pro přizpůsobení uživatelského rozhraní stránky | Microsoft Docs'
description: Pomocný nástroj demonstrovat funkce přizpůsobení uživatelského rozhraní stránky v Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.openlocfilehash: de3d430ad984c9b173f77138417532badc3a7497
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active Directory B2C: Pomocná nástroj používaný k předvedení stránce uživatelské rozhraní (UI) přizpůsobení funkce
Tento článek je Pomocníka pro [hlavní článek přizpůsobení uživatelského rozhraní](active-directory-b2c-reference-ui-customization.md) v Azure Active Directory (Azure AD) B2C. Následující kroky popisují postup vykonávat funkci přizpůsobení uživatelského rozhraní stránky pomocí ukázkový kód HTML a CSS obsah, který nabízíme.

## <a name="get-an-azure-ad-b2c-tenant"></a>Získání klienta Azure AD B2C
Před úpravou nic, budete muset [získat klienta Azure AD B2C](active-directory-b2c-get-started.md) Pokud jste ještě nemáte.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Vytvoření zásady registrace nebo přihlašování
Ukázkový obsah nabízíme slouží k customze dvě stránky v [zásady registrace nebo přihlášení](active-directory-b2c-reference-policies.md): [jednotná přihlašovací stránce](active-directory-b2c-reference-ui-customization.md) a [samoobslužné uplatňovaná atributy stránky](active-directory-b2c-reference-ui-customization.md). Když [vytváření zásad vaše registrace nebo přihlášení](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), přidejte místní účet (e-mailovou adresu), Facebook, Google a Microsoft jako **zprostředkovatelů Identity**. Toto jsou pouze IDPs, které bude přijímat naše ukázka obsah HTML.  Můžete také přidat podmnožinu těchto IDPs, nechcete-li.

## <a name="register-an-application"></a>Registrace aplikace
Budete muset [zaregistrovat aplikaci](active-directory-b2c-app-registration.md) v svého klienta B2C, který můžete použít ke spuštění vaše zásady. Po registraci vaší aplikace, máte několik možností, které můžete použít ke spuštění ve skutečnosti svojí registrační zásadě:

* Jeden z Azure AD B2C úvodní aplikace uvedené v části "Začínáme" sestavení [přihlásit registrace a přihlašování uživatelů aplikace](active-directory-b2c-overview.md).
* Použijte předem připravený [Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net) aplikace. Pokud chcete použít playground, je nutné zaregistrovat aplikaci v svého klienta B2C pomocí **identifikátor URI pro přesměrování** `https://aadb2cplayground.azurewebsites.net/`.
* Použití **spustit nyní** na vaše zásady v tlačítko [portál Azure](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Přizpůsobit zásady
Chcete-li přizpůsobit vzhled a chování zásad, musíte nejprve vytvořit soubory HTML a CSS pomocí konkrétní konvencí Azure AD B2C. Potom můžete nahrát statický obsah na veřejně dostupné místo, se kterým můžete Azure AD B2C. To může být vlastní vyhrazený webový server, Azure Blob Storage, Azure Content Delivery Network nebo jakékoli jiné statické hostování prostředků zprostředkovatele. Jediným požadavkem je, že obsah je k dispozici prostřednictvím protokolu HTTPS a je přístupný pomocí CORS. Jakmile jste vystavený statický obsah na webu, můžete upravit zásady vaší bod do tohoto umístění a prezentovat obsah vašich zákazníků. [Hlavní článek přizpůsobení uživatelského rozhraní](active-directory-b2c-reference-ui-customization.md) podrobně popisuje, jak funguje přizpůsobení funkce Azure AD B2C.

Pro účely tohoto kurzu byl již vytvořen některé ukázkový obsah a hostované v Azure Blob Storage. Ukázkový obsah je velmi základní přizpůsobení v motivu fiktivní společnosti "Adresář Wingtip Toys". Můžete vyzkoušet na ve vlastních zásad, postupujte takto:

1. Přihlaste se ke klientovi [portál Azure](https://portal.azure.com/) a přejděte do okna s funkcemi B2C.
2. Klikněte na tlačítko **zásady registrace nebo přihlášení**vaše zásady a klikněte na Upravit (například "b2c\_1\_přihlašovací\_až\_přihlašovací\_v").
3. Klikněte na tlačítko **přizpůsobení uživatelského rozhraní stránky** a potom **stránku registrace nebo přihlášení Unified**.
4. Přepnutí **použití vlastní stránku** přepnout **Ano**. V **vlastní stránku URI** zadejte `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Klikněte na **OK**.
5. Klikněte na tlačítko **stránku pro přihlášení místní účet**. Přepnutí **použít vlastní šablonu** přepnout **Ano**. V **vlastní stránku URI** zadejte `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Opakujte stejný krok pro **stránku pro přihlášení sociálních účet**.
   Klikněte na tlačítko **OK** zavřete okna přizpůsobení uživatelského rozhraní.
7. Klikněte na **Uložit**.

Nyní můžete vyzkoušet vaše vlastní zásady. Pokud chcete, ale můžete taky jednoduše klikněte na tlačítko můžete použít vlastní aplikaci nebo Azure AD B2C playground **spustit nyní** příkazu v okně zásady. V rozevíracím seznamu vyberte aplikaci a vyberte odpovídající identifikátor URI pro přesměrování. Klikněte **spustit nyní** tlačítko. Otevřete novou kartu prohlížeče a mohou být spuštěny prostřednictvím činnost koncového uživatele registrace do vaší aplikace pomocí nový obsah v místní!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Tento ukázkový obsah nahrát do Azure Blob Storage
Pokud chcete používat úložiště objektů Blob Azure k hostování obsahu stránce, můžete vytvořit účet úložiště a použít k nahrání souborů pomocným nástrojem pro naše B2C.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **+ nový** > **Data + úložiště** > **účet úložiště**. Budete potřebovat předplatné Azure k vytvoření účtu Azure Blob Storage. Můžete si zaregistrovat bezplatnou zkušební verzi na [webu Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Zadejte **název** pro úložiště účtu (například "contoso") a vyberte odpovídající možnosti pro **cenová úroveň**, **skupiny prostředků** a  **Předplatné**. Ujistěte se, že máte **připnout na úvodní panel** možnost zaškrtnutí. Klikněte na možnost **Vytvořit**.
4. Přejděte zpět na úvodní panel a klikněte na účet úložiště, který jste právě vytvořili.
5. V **Souhrn** klikněte na tlačítko **kontejnery**a potom klikněte na **+ přidat**.
6. Zadejte **název** pro kontejner (například "b2c") a vyberte **Blob** jako **přistupovat typu**. Klikněte na **OK**.
7. Kontejner, který jste vytvořili se zobrazí v seznamu na **objekty BLOB** okno. Poznamenejte si adresu URL kontejneru; například by měl vypadat podobně jako `https://contoso.blob.core.windows.net/b2c`. Zavřít **objekty BLOB** okno.
8. V okně účtu úložiště, klikněte na **klíče** a poznamenejte si hodnoty **název účtu úložiště** a **primární přístupový klíč** pole.

> [!NOTE]
> **Primární přístupový klíč** je důležitým bezpečnostním pověřením.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>Stažení souborů nástroj a ukázkové pomocné rutiny
Si můžete stáhnout [Azure Blob Storage pomocné rutiny nástroje a ukázkové soubory jako soubor ZIP](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) nebo klonovat z Githubu:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Toto úložiště obsahuje `sample_templates\wingtip` adresáři, který obsahuje příklad HTML, CSS a obrázků. Pro tyto šablony k odkazování svůj vlastní účet Azure Blob Storage musíte upravit soubory HTML. Otevřete `unified.html` a `selfasserted.html` a nahraďte všechny výskyty `https://localhost` s adresou URL vlastní kontejner, který jste si poznamenali v předchozím kroku. Absolutní cesta souborů HTML musíte použít, protože v takovém případě se službou Azure AD, v rámci domény zpracuje HTML `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Odeslání ukázkových souborů
V úložišti stejné rozbalte `B2CAzureStorageClient.zip` a spusťte `B2CAzureStorageClient.exe` souboru v rámci. Tento program bude jednoduše nahrát všechny soubory v adresáři, který určíte na váš účet úložiště a povolení CORS přístup pro tyto soubory. Pokud jste postupovali podle kroků výše, soubory HTML a CSS teď přejdete k vašemu účtu úložiště. Všimněte si, zda je název vašeho účtu úložiště část, která předchází `blob.core.windows.net`, například `contoso`. Můžete ověřit, že obsah se nahrají správně podle pokusu o přístup k `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` v prohlížeči. Použít také [ http://test-cors.org/ ](http://test-cors.org/) a ujistěte se, že obsah je nyní povolení CORS. (Vyhledejte "stav XHR: 200" ve výsledku.)

### <a name="customize-your-policy-again"></a>Přizpůsobit zásady vaší znovu
Teď, když si ukázkový obsah jste odeslali do účtu úložiště, je nutné upravit svojí registrační zásadě na něj odkazovat. Opakujte kroky z ["Přizpůsobit vaše zásady"](#customize-your-policy) části výše, tentokrát pomocí adresy URL svůj vlastní účet úložiště. Například umístění vaší `unified.html` souboru by `<url-of-your-container>/wingtip/unified.html`.

Nyní můžete pomocí **spustit nyní** tlačítko nebo vlastní aplikaci a znovu spustit vaše zásady. Výsledek by měl vypadat téměř úplně stejné – jste použili stejné v obou případech ukázkový kód HTML a CSS. Ale zásad nyní odkazují na vlastní instanci Azure Blob Storage a můžete upravit a prosím odesílat soubory, jako je znovu. Další informace o přizpůsobení HTML a CSS, najdete v části [hlavní článek přizpůsobení uživatelského rozhraní](active-directory-b2c-reference-ui-customization.md).

