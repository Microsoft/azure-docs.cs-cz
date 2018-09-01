---
title: Pomocným nástrojem pro přizpůsobení uživatelského rozhraní stránky v Azure Active Directory B2C | Dokumentace Microsoftu
description: Pomocný nástroj použitá k předvedení funkcí přizpůsobení uživatelského rozhraní stránky v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1a37a37dbed3b5ef9733f1105444529b4d255bcf
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336777"
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active Directory B2C: Pomocným nástrojem pro použitá k předvedení funkci přizpůsobení stránky uživatelského rozhraní (UI)
Tento článek, který je k [hlavní článek přizpůsobení uživatelského rozhraní](active-directory-b2c-reference-ui-customization.md) v Azure Active Directory (Azure AD) B2C. Následující kroky popisují, jak vykonávat funkci přizpůsobení uživatelského rozhraní stránky s použitím ukázky kódu HTML a CSS obsah, který nabízíme.

## <a name="get-an-azure-ad-b2c-tenant"></a>Získání tenanta služby Azure AD B2C
Před úpravou nic, budete muset [získání tenanta služby Azure AD B2C](active-directory-b2c-get-started.md) pokud ho ještě nemáte.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Vytvoření zásady registrace nebo přihlašování
Ukázkový obsah, poskytujeme je možné customze dvě stránky [zásady registrace / přihlášení](active-directory-b2c-reference-policies.md): [jednotné přihlašovací stránku](active-directory-b2c-reference-ui-customization.md) a [stránky s vlastním potvrzením atributy](active-directory-b2c-reference-ui-customization.md). Když [vytvořením zásady registrace / přihlášení](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), přidejte místní účet (e-mailová adresa), Facebook, Google a Microsoft jako **zprostředkovatelé Identity**. Jedná se o pouze zprostředkovatelů identity, která bude přijímat náš ukázkový obsah HTML.  Pokud chcete, můžete také přidat podmnožinu těchto zprostředkovatelů identity.

## <a name="register-an-application"></a>Registrace aplikace
Budete muset [zaregistrovat aplikaci](active-directory-b2c-app-registration.md) ve vašem tenantovi B2C, který slouží ke spuštění vaší zásady. Po registraci vaší aplikace, máte několik možností, které můžete použít ke spuštění ve skutečnosti svojí registrační zásadě:

* Sestavit jeden z Azure AD B2C úvodní aplikace uvedené v části "Začínáme" [přihlášení a uživatelů do vašich aplikací přihlašování](active-directory-b2c-overview.md).
* Použijte předem sestavené [Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net) aplikace. Pokud se rozhodnete použít playground, musí si zaregistrujete aplikaci ve vašem tenantovi B2C pomocí **identifikátor URI pro přesměrování** `https://aadb2cplayground.azurewebsites.net/`.
* Použití **Run Now** tlačítko na vašich zásadách v [webu Azure portal](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Upravit zásady
Přizpůsobení vzhledu a chování vašich zásad, musíte nejdřív vytvořit soubory HTML a CSS pomocí konkrétní konvencí Azure AD B2C. Pak můžete nahrát statického obsahu na veřejně dostupné místo tak, aby Azure AD B2C k němu máte přístup. To může být svůj vlastní vyhrazený webový server, Azure Blob Storage, Azure Content Delivery Network nebo jakýkoli jiný statický prostředek hostování poskytovatel. Jediným požadavkem je, že váš obsah je k dispozici prostřednictvím protokolu HTTPS a je přístupný pomocí CORS. Když jste vystavený váš statický obsah na webu, můžete upravit zásady odkazoval na toto umístění a prezentovat obsah svým zákazníkům. [Hlavní článek přizpůsobení uživatelského rozhraní](active-directory-b2c-reference-ui-customization.md) podrobně popisuje, jak funguje funkce vlastního nastavení Azure AD B2C.

Pro účely tohoto kurzu jsme už vytvořili některé ukázkový obsah a hostované v Azure Blob Storage. Ukázkový obsah je velmi základní vlastní nastavení v motivu fiktivní společnost "Na adresář Wingtip Toys". Vyzkoušejte si to ve vlastních zásad, postupujte takto:

1. Přihlaste se k vašemu tenantovi [webu Azure portal](https://portal.azure.com/) a přejděte do okna s funkcemi B2C.
2. Klikněte na tlačítko **zásady registrace nebo přihlášení**zásady a klikněte na Upravit (například "b2c\_1\_přihlašování\_nahoru\_přihlašování\_v").
3. Klikněte na tlačítko **přizpůsobení uživatelského rozhraní stránky** a potom **stránku registrace nebo přihlášení Unified**.
4. Přepnout **použít vlastní stránku** přepnout na **Ano**. V **vlastní identifikátor URI stránky** zadejte `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Klikněte na **OK**.
5. Klikněte na tlačítko **stránku pro přihlášení místním účtem**. Přepnout **použít vlastní šablonu** přepnout na **Ano**. V **vlastní identifikátor URI stránky** zadejte `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Opakujte stejný krok pro **stránka registrace sociálního účtu**.
   Klikněte na tlačítko **OK** dvakrát na pozavírejte tato přizpůsobení uživatelského rozhraní.
7. Klikněte na **Uložit**.

Nyní můžete vyzkoušet vlastní zásady. Pokud chcete, ale můžete také jednoduše klikněte na tlačítko můžete použít svoji vlastní aplikaci nebo Azure AD B2C playground **Run Now** příkazu v okně zásady. V rozevíracím seznamu vyberte svoji aplikaci a vyberte odpovídající identifikátor URI pro přesměrování. Klikněte na tlačítko **spustit nyní** tlačítko. Otevře se nová karta prohlížeče a můžete si projít uživatelské prostředí pro registraci aplikace s použitím nového obsahu na místě!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Nahrajte ukázkový obsah do úložiště objektů Blob v Azure
Pokud chcete používat úložiště objektů Blob v Azure k hostování obsahu stránky, můžete vytvořit svůj vlastní účet úložiště a použít pomocným nástrojem pro naše B2C k nahrání souborů.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **+ nová** > **Data + úložiště** > **účtu úložiště**. Budete potřebovat předplatné Azure k vytvoření účtu služby Azure Blob Storage. Můžete se zaregistrovat k bezplatné zkušební verzi na [web Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Zadejte **název** pro úložiště účtu (například "contoso") a vyberte odpovídající možnosti pro **cenová úroveň**, **skupiny prostředků** a  **Předplatné**. Ujistěte se, že máte **připnout na úvodní panel** zaškrtnutým políčkem. Klikněte na možnost **Vytvořit**.
4. Přejděte zpět na úvodní panel a klikněte na účet úložiště, který jste právě vytvořili.
5. V **Souhrn** klikněte na tlačítko **kontejnery**a potom klikněte na tlačítko **+ přidat**.
6. Zadejte **název** kontejneru (například "b2c") a vyberte **Blob** jako **získat přístup k typu**. Klikněte na **OK**.
7. Kontejner, který jste vytvořili, se zobrazí v seznamu na **objekty BLOB** okno. Poznamenejte si adresu URL kontejneru; například by měla vypadat podobně jako `https://contoso.blob.core.windows.net/b2c`. Zavřít **objekty BLOB** okno.
8. V okně účtu úložiště klikněte na tlačítko **klíče** a poznamenejte si hodnoty **název účtu úložiště** a **primární přístupový klíč** pole.

> [!NOTE]
> **Primární přístupový klíč** je důležitým bezpečnostním pověřením.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>Stažení souborů pomocné nástroje a ukázky
Můžete stáhnout [soubory nástroje a ukázky pomocné rutiny služby Azure Blob Storage jako soubor ZIP](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) nebo jej klonovat z Githubu:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Toto úložiště obsahuje `sample_templates\wingtip` adresáře, který obsahuje příklad HTML, CSS a obrázků. Pro tyto šablony k odkazování účtu služby Azure Blob Storage budete muset upravit soubory HTML. Otevřít `unified.html` a `selfasserted.html` a nahraďte všechny výskyty `https://localhost` adresou URL vlastní kontejner, který jste si poznamenali v předchozím kroku. Musíte použít absolutní cesta souborů HTML, protože v takovém případě bude obsluhovat HTML pomocí Azure AD v rámci domény `tenantname.b2clogin.com`.

### <a name="upload-the-sample-files"></a>Nahrání ukázkových souborů
Ve stejném úložišti, rozbalte `B2CAzureStorageClient.zip` a spustit `B2CAzureStorageClient.exe` soubor. Tento program bude jednoduše nahrát všechny soubory v adresáři, který zadáte do svého účtu úložiště a povolení CORS přístup k těmto souborům. Pokud jste postupovali podle výše uvedených kroků, soubory HTML a CSS nyní odkazuje na svůj účet úložiště. Všimněte si, že název účtu úložiště je část, která předchází `blob.core.windows.net`; například `contoso`. Můžete ověřit, že obsah je nahraná správně tak, že zkusíte přístup `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` v prohlížeči. Použít také [ http://test-cors.org/ ](http://test-cors.org/) abyste měli jistotu, že obsah je teď zapnuté CORS. (Vyhledejte "XHR stav: 200" ve výsledcích.)

### <a name="customize-your-policy-again"></a>Přizpůsobení vaše zásady znovu
Teď, když ukázkový obsah nahrajete do účtu úložiště, je nutné upravit odkaz ve svojí registrační zásadě. Zopakováním postupu z ["Upravit zásady"](#customize-your-policy) části výše, tentokrát pomocí adresy URL vlastní účet úložiště. Například umístění vašeho `unified.html` souboru by `<url-of-your-container>/wingtip/unified.html`.

Teď můžete použít **Run Now** tlačítko nebo vlastní aplikaci a znovu spustit vaše zásady. Výsledek by měl vypadat téměř stejně,--můžete použít v obou případech stejný ukázkový HTML a CSS. Ale zásady jsou nyní odkazuje na vlastní instanci služby Azure Blob Storage a můžete libovolně upravovat a nahrávat soubory znovu jako prosím. Další informace o přizpůsobení HTML a CSS, najdete [hlavní článek přizpůsobení uživatelského rozhraní](active-directory-b2c-reference-ui-customization.md).

