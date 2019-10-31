---
title: Přizpůsobení uživatelského rozhraní cesty uživatelů pomocí vlastních zásad | Microsoft Docs
description: Přečtěte si o Azure Active Directory B2C vlastních zásadách.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7e4714de9868dbd540e2e662b22a22da6df6514b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147528"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Přizpůsobení uživatelského rozhraní cesty uživatelů pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Tento článek je pokročilý popis způsobu, jakým funguje přizpůsobení uživatelského rozhraní a jak pomocí Azure AD B2C vlastní zásady, a to s využitím architektury identity Experience Framework.


Snadné uživatelské prostředí je klíč pro jakékoli řešení od firmy k spotřebiteli. Bezproblémové uživatelské prostředí je prostředí, bez ohledu na to, jestli je na zařízení nebo v prohlížeči, kde cestu uživatele prostřednictvím služby nerozlišujeme od služby zákazníkům, které používají.

## <a name="understand-the-cors-way-for-ui-customization"></a>Porozumění způsobům přizpůsobení uživatelského rozhraní pro CORS

Azure AD B2C vám umožní přizpůsobit vzhled uživatelského prostředí (UX) na různých stránkách, které se zpracovávají a zobrazují Azure AD B2C pomocí vlastních zásad.

Pro tento účel Azure AD B2C spouští kód v prohlížeči vašeho zákazníka a používá moderní a standardní přístup ke [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) k načtení vlastního obsahu z konkrétní adresy URL, kterou zadáte v vlastní zásadě, aby odkazovala na HTML5/CSS. šablony. CORS je mechanismus, který umožňuje omezený přístup k prostředkům, jako jsou písma, na webové stránce, která se požaduje z jiné domény mimo doménu, ze které pochází prostředek.

V porovnání se starým tradičním způsobem, kde stránky šablon vlastní řešení, ve kterém jste zadali omezený text a obrázky, kde omezená kontrola rozložení a chování byla nabídnuta tak, aby bylo zajištěno bezproblémové prostředí, CORS podporuje HTML5 a CSS a umožňuje:

- Hostování obsahu a řešení vloží své ovládací prvky pomocí skriptu na straně klienta.
- Mít plnou kontrolu nad každým pixelem rozložení a chováním.

Můžete zadat libovolný počet stránek obsahu podle potřeby tím, že budete chtít soubory HTML5/CSS vytvořit podle potřeby.

> [!NOTE]
> Z bezpečnostních důvodů je použití JavaScriptu v současnosti blokované pro přizpůsobení. 

V každé z šablon HTML5/CSS poskytnete prvek *ukotvení* , který odpovídá požadovanému prvku `<div id="api">` v HTML nebo na stránce obsahu, jak je znázorněno níže. Azure AD B2C vyžaduje, aby všechny stránky obsahu měly tento konkrétní div.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

Obsah související s Azure AD B2C pro stránku je vložen do tohoto div, zatímco zbytek stránky je váš ovládací prvek. Kód jazyka JavaScript Azure AD B2C v obsahu vyžádá a vloží do tohoto specifického prvku div kód HTML. Azure AD B2C vloží následující ovládací prvky podle potřeby: ovládací prvek Výběr účtu, ovládací prvky pro přihlášení, Multi-Factor (aktuálně založené na telefonu) a ovládací prvky kolekce atributů. Azure AD B2C zajistí, že všechny ovládací prvky jsou kompatibilní s HTML5 a jsou přístupné, všechny ovládací prvky mohou být plně ve stylu a že verze ovládacího prvku nevrátí hodnotu.

Sloučený obsah se nakonec zobrazí jako dynamický dokument pro vašeho příjemce.

Abyste měli jistotu, že všechno funguje podle očekávání, musíte:

- Ujistěte se, že váš obsah je kompatibilní s HTML5 a je přístupný.
- Ujistěte se, že je server obsahu pro CORS povolený.
- Slouží k poskytování obsahu prostřednictvím protokolu HTTPS.
- Pro všechny odkazy a obsah CSS použijte absolutní adresy URL, jako je `https://yourdomain/content`.

> [!TIP]
> Chcete-li ověřit, zda web, na kterém je hostitelem vašeho obsahu, je povolen a testována žádost CORS, můžete použít https://test-cors.org/ lokality. Díky tomuto webu můžete buď Odeslat žádost CORS vzdálenému serveru (otestovat, jestli je CORS podporovaná), nebo poslat požadavek CORS na testovací server (abyste prozkoumali určité funkce CORS).

> [!TIP]
> Lokalita https://enable-cors.org/ také představuje více než užitečných prostředků v CORS.

Díky tomuto přístupu založenému na CORS mají koncoví uživatelé konzistentní prostředí mezi vaší aplikací a stránkami, které obsluhuje Azure AD B2C.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

V takovém případě je potřeba vytvořit účet úložiště. K vytvoření účtu Azure Blob Storage potřebujete předplatné Azure. Bezplatnou zkušební verzi si můžete zaregistrovat na [webu Azure](https://azure.microsoft.com/pricing/free-trial/).

1. Otevřete relaci procházení a přejděte na [Azure Portal](https://portal.azure.com).
2. Přihlaste se pomocí přihlašovacích údajů správce.
3. Klikněte na **vytvořit prostředek** > **úložiště** > **účet úložiště**.  Otevře se podokno **vytvořit účet úložiště** .
4. Do pole **název**zadejte název účtu úložiště, například *contoso369b2c*. Tato hodnota se později označuje jako *storageAccountName*.
5. Vyberte vhodné možnosti pro cenovou úroveň, skupinu prostředků a předplatné. Ujistěte se, že máte zaškrtnuté políčko **Připnout na úvodní panel** . Klikněte na **Vytvořit**.
6. Vraťte se na úvodní panel a klikněte na účet úložiště, který jste vytvořili.
7. V části **služby** klikněte na **objekty blob**. Otevře se **podokno BLOB Service** .
8. Klikněte na **+ kontejner**.
9. Do pole **název**zadejte název kontejneru, například *B2C*. Tato hodnota se později označuje jako *ContainerName*.
9. Jako **typ přístupu**vyberte **objekt BLOB** . Klikněte na **Vytvořit**.
10. Kontejner, který jste vytvořili, se zobrazí v seznamu v **podokně BLOB Service**.
11. Zavřete podokno **objekty blob** .
12. V **podokně účet úložiště**klikněte na ikonu **klíče** . Otevře se **podokno přístupové klíče** .  
13. Zapište hodnotu **klíč1**. Tato hodnota se později označuje jako *klíč1*.

## <a name="downloading-the-helper-tool"></a>Stažení podpůrného nástroje

1.  Stáhněte si pomocný nástroj z [GitHubu](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Uložte soubor *B2C-AzureBlobStorage-Client-Master. zip* do místního počítače.
3.  Extrahujte obsah souboru B2C-AzureBlobStorage-Client-master. zip na místním disku, například ve složce **uživatelského rozhraní – přizpůsobení sady Pack** , která vytvoří složku *B2C-AzureBlobStorage-Client-Master* pod uzlem.
4.  Otevřete tuto složku a extrahujte obsah souboru archivu *B2CAzureStorageClient. zip* .

## <a name="upload-the-ui-customization-pack-sample-files"></a>Nahrání ukázkových souborů uživatelského rozhraní a přizpůsobení balíčku

1.  Pomocí Průzkumníka Windows přejděte do složky *B2C-AzureBlobStorage-Client-Master* umístěný v části *uživatelské rozhraní – přizpůsobení složky balíčku* vytvořené v předchozí části.
2.  Spusťte soubor *B2CAzureStorageClient. exe* . Tento program nahraje všechny soubory v adresáři, který zadáte do svého účtu úložiště, a povolí přístup CORS pro tyto soubory.
3.  Po zobrazení výzvy zadejte: a.  Název vašeho účtu úložiště *storageAccountName*, například *contoso369b2c*.
    b.  Primární přístupový klíč úložiště objektů BLOB v Azure ( *klíč1*), například *contoso369b2c*.
    c.  Název kontejneru úložiště objektů BLOB *úložiště, například* *B2C*.
    d.  Cesta k ukázkovým souborům *Úvodní sady* , například *. \B2CTemplates\wingtiptoys*.

Pokud jste postupovali podle předchozích kroků, soubory HTML5 a CSS *uživatelského rozhraní přizpůsobení* sady pro fiktivní společnost **wingtiptoys** nyní ukazují na váš účet úložiště.  Chcete-li ověřit, zda byl obsah správně nahrán, otevřete související podokno kontejneru v Azure Portal. Můžete také ověřit, zda byl obsah správně nahrán přístupem k stránce z prohlížeče. Další informace najdete v tématu [Azure Active Directory B2C: pomocný nástroj, který slouží k předvedení funkce přizpůsobení uživatelského rozhraní stránky](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Ujistěte se, že účet úložiště má povolené CORS.

Aby bylo možné Azure AD B2C načíst obsah, musí být ve vašem koncovém bodu povolená CORS (sdílení prostředků mezi zdroji). Je to proto, že váš obsah je hostovaný v jiné doméně než doména Azure AD B2C bude tato stránka obsluhovat.

Chcete-li ověřit, zda úložiště, na kterém je hostitelem vašeho obsahu, je povoleno CORS, postupujte podle následujících kroků:

1. Otevřete relaci procházení a přejděte na stránku *Unified. html* pomocí úplné adresy URL svého umístění v účtu úložiště `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Například, https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Přejděte na https://test-cors.org. Tato lokalita vám umožní ověřit, jestli je na stránce, kterou používáte, povolená CORS.  
   <!--
   ![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
   -->

3. Do pole **Vzdálená adresa URL**zadejte úplnou adresu URL vašeho sjednoceného obsahu. html a klikněte na **Odeslat žádost**.
4. Ověřte, že výstup v části **Results** obsahuje *stav XHR: 200*, který indikuje, že Cors je povolená.
   <!--
   ![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
   -->
   Účet úložiště by měl nyní obsahovat kontejner objektů BLOB s názvem *B2C* na ilustraci, který obsahuje následující šablony wingtiptoys z *Úvodní sady*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

Následující tabulka popisuje účel předchozí stránky HTML5.

| Šablona HTML5 | Popis |
|----------------|-------------|
| *PhoneFactor. html* | Tuto stránku lze použít jako šablonu pro stránku služby Multi-Factor Authentication. |
| *ResetPassword. html* | Tuto stránku lze použít jako šablonu pro stránku zapomenutého hesla. |
| *selfasserted. html* | Tuto stránku lze použít jako šablonu pro přihlašovací stránku účtu sociální sítě, přihlašovací stránku místního účtu nebo přihlašovací stránku místního účtu. |
| *Unified. html* | Tuto stránku lze použít jako šablonu pro sjednocenou registraci nebo přihlašovací stránku. |
| *updateprofile. html* | Tuto stránku lze použít jako šablonu pro stránku Aktualizace profilu. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Přidání odkazu na vaše šablony HTML5/CSS na cestu uživatele

Můžete přidat odkaz na vaše šablony HTML5/CSS na cestu uživatele úpravou vlastních zásad přímo.

Vlastní šablony HTML5/CSS, které se mají použít v cestě uživatele, je třeba zadat v seznamu definic obsahu, které lze použít v těchto uživatelských cestách. Pro tento účel musí být v části *\<BuildingBlocks >* souboru XML s vlastními zásadami deklarovány volitelné\<elementu xml *ContentDefinitions >* .

Následující tabulka popisuje sadu ID definic obsahu rozpoznávaných modulem Azure AD B2C identity Experience a typ stránek, které se na ně vztahují.

| ID definice obsahu | Popis |
|-----------------------|-------------|
| *rozhraní API. Chyba* | **Chybová stránka**. Tato stránka se zobrazí, pokud dojde k výjimce nebo chybě. |
| *API. idpselections* | **Stránka výběru zprostředkovatele identity** Tato stránka obsahuje seznam zprostředkovatelů identity, ze kterých si může uživatel vybrat během přihlašování. Tito poskytovatelé jsou buď poskytovatelé podnikových identit, zprostředkovatelé sociálních identit, jako je Facebook nebo Google +, nebo místní účty (na základě e-mailové adresy nebo uživatelského jména). |
| *API. idpselections. signup* | **Výběr zprostředkovatele identity pro registraci**. Tato stránka obsahuje seznam zprostředkovatelů identity, ze kterých si může uživatel vybrat během registrace. Tito poskytovatelé jsou buď poskytovatelé podnikových identit, zprostředkovatelé sociálních identit, jako je Facebook nebo Google +, nebo místní účty (na základě e-mailové adresy nebo uživatelského jména). |
| *API. localaccountpasswordreset* | **Stránka zapomenuté heslo** Tato stránka obsahuje formulář, který uživatel musí vyplnit, aby inicializoval resetování hesla.  |
| *API. localaccountsignin* | **Přihlašovací stránka místního účtu** Tato stránka obsahuje přihlašovací formulář, který musí uživatel vyplnit při přihlašování pomocí místního účtu, který je založený na e-mailové adrese nebo uživatelském jménu. Formulář může obsahovat textové pole pro zadání textu a heslo. |
| *API. localaccountsignup* | **Přihlašovací stránka místního účtu**. Tato stránka obsahuje registrační formulář, který musí uživatel vyplnit při registraci místního účtu na základě e-mailové adresy nebo uživatelského jména. Formulář může obsahovat různé vstupní ovládací prvky, jako je textové pole, vstupní pole pro heslo, přepínač, rozevírací seznamy s jedním výběrem a zaškrtávací políčka vícenásobného výběru. |
| *API. PhoneFactor* | **Stránku Multi-Factor Authentication**. Na této stránce můžou uživatelé při registraci nebo přihlašování ověřit svoje telefonní čísla (pomocí textu nebo hlasu). |
| *API. selfasserted* | **Stránka pro registraci účtu sociální**sítě Tato stránka obsahuje registrační formulář, který musí uživatel vyplnit při registraci pomocí existujícího účtu od poskytovatele sociálních identit, jako je Facebook nebo Google +. Tato stránka se podobá předchozí stránce pro registraci účtu sociální sítě s výjimkou polí zadání hesla. |
| *API. selfasserted. profileupdate* | **Stránka aktualizace profilu**. Tato stránka obsahuje formulář, pomocí kterého může uživatel aktualizovat svůj profil. Tato stránka se podobá předchozí stránce pro registraci účtu sociální sítě s výjimkou polí zadání hesla. |
| *API. signuporsignin* | **Jednotná registrace nebo přihlašovací stránka**.  Tato stránka zpracovává jak registraci, tak & přihlašování uživatelů, kteří můžou používat podnikové zprostředkovatele identity, poskytovatele sociálních identit, jako je Facebook nebo Google + nebo místní účty.

## <a name="next-steps"></a>Další kroky
[Referenční informace: princip fungování vlastních zásad s architekturou prostředí identity v B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
