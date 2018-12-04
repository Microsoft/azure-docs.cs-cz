---
title: Přizpůsobení uživatelského rozhraní cesty uživatele pomocí vlastních zásad | Dokumentace Microsoftu
description: Další informace o vlastních zásadách Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8c3684f93bbf5b9fe9f5ea9167396b9822e70c48
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841621"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Přizpůsobení uživatelského rozhraní cesty uživatele pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Tento článek je pokročilá popis fungování přizpůsobení uživatelského rozhraní a jak povolit vlastní zásady Azure AD B2C, pomocí architekturu rozhraní identit.


Integrované uživatelské prostředí je klíčem k jakékoli řešení business-to-consumer. Integrované uživatelské prostředí je prostředí, ať už na zařízení nebo prohlížeče, kde je nerozeznatelná od služby zákazníka, který používají cesty uživatele přes službu.

## <a name="understand-the-cors-way-for-ui-customization"></a>Pochopení způsobu CORS pro přizpůsobení uživatelského rozhraní

Azure AD B2C umožňuje přizpůsobit vzhled a chování uživatelského rozhraní (UX) na různých stránkách, které se obsluhují a službou Azure AD B2C zobrazí pomocí vlastních zásad.

Pro tento účel, Azure AD B2C, spustí kód v prohlížeči vašich uživatelů a používá moderní a standardní přístup [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) načíst z konkrétní adresy URL, který zadáte do vlastní zásady tak, aby odkazoval na vlastní obsah šablony HTML5 a CSS. CORS je mechanismus, který umožní omezené prostředky, například písma, na webové stránce vyžadované z jiné domény mimo doménu, ze kterého se vytvoří prostředek.

Porovnání s tradičním způsobem staré, kde stránky šablon jsou vlastněny řešení, které jste zadali omezená textu a obrázků, kde byly nabízeny omezené možnosti kontroly rozložení a vzhled, což vede k více než potíže zajistit bezproblémové prostředí tak, jak CORS podporuje HTML5 a CSS a umožňují:

- Hostování obsahu a řešení vkládá jeho ovládacích prvků pomocí skriptu na straně klienta.
- Máte plnou kontrolu nad každý pixel rozložení a vzhled.

Můžete zadat libovolný počet stránky obsahu, jako například tím, že vytvoří soubory HTML5/CSS podle potřeby.

> [!NOTE]
> Z bezpečnostních důvodů použití jazyka JavaScript a aktuálně je blokováno pro přizpůsobení. 

V každém ze šablony HTML5 a CSS, poskytnete *ukotvení* element, který odpovídá požadovaným `<div id=”api”>` prvek jazyka HTML nebo stránky obsahu, tak, jak ukazuje níže. Azure AD B2C vyžaduje, aby měly všechny stránky obsahu této konkrétní div.

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

Azure AD B2C související obsah stránky se vloží do tento element div při zbytku stránky je váš ovládací prvek. Kód jazyka JavaScript v Azure AD B2C si vyžádá obsah a vloží do této konkrétní div elementu HTML. Azure AD B2C vkládá následující ovládací prvky podle potřeby: ovládací prvek pro výběr účtu, přihlaste se službou Multi-Factor Authentication (aktuálně založené na telefonu) ovládacích prvků a ovládací prvky kolekce atributu. Azure AD B2C zajišťuje, že všechny ovládací prvky jsou HTML5 kompatibilní a přístupné, všechny ovládací prvky můžete plně styl a, která verze ovládacího prvku nelze vrátit.

Sloučené obsah se nakonec zobrazí jako dynamický dokument, aby se vaše.

Aby bylo zajištěno, že vše funguje podle očekávání, musíte mít:

- Zajistěte, aby byl váš obsah HTML5 kompatibilní a přístupné
- Ujistěte se, že vaše servery obsahu je povolený pro sdílení CORS.
- Poskytování obsahu prostřednictvím protokolu HTTPS.
- Použít absolutní adresy URL například https://yourdomain/content pro všechny odkazy a obsah šablony stylů CSS.

> [!TIP]
> Ověřte, že hostují váš obsah na lokalitu má zapnuté CORS a testování požadavků CORS, můžete použít web https://test-cors.org/. Díky této lokalitě můžete odeslat požadavek CORS ke vzdálenému serveru (který testuje, jestli se podporuje CORS) nebo odeslat požadavek CORS na testovací server (Chcete-li prozkoumat některé funkce CORS).

> [!TIP]
> Webu https://enable-cors.org/ také se považuje za více než užitečné zdroje informací o CORS.

Díky tomu na základě CORS koncoví uživatelé měli konzistentní prostředí mezi vaší aplikací a stránky, které obsluhuje Azure AD B2C.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Předpokladem je budete muset vytvořit účet úložiště. Budete potřebovat předplatné Azure k vytvoření účtu služby Azure Blob Storage. Můžete se zaregistrovat k bezplatné zkušební verzi na [web Azure](https://azure.microsoft.com/pricing/free-trial/).

1. Otevřete relaci prohlížeče a přejděte [webu Azure portal](https://portal.azure.com).
2. Přihlaste se pomocí přihlašovacích údajů správce.
3. Klikněte na tlačítko **vytvořit prostředek** > **úložiště** > **účtu úložiště**.  A **vytvořit účet úložiště** otevře podokno.
4. V **název**, zadejte název účtu úložiště, například *contoso369b2c*. Tato hodnota je později uvedené jako *storageAccountName*.
5. Vyberte odpovídající možnosti pro cenovou úroveň, skupinu prostředků a předplatném. Ujistěte se, že máte **připnout na úvodní panel** zaškrtnutým políčkem. Klikněte na možnost **Vytvořit**.
6. Přejděte zpět na úvodní panel a klikněte na účet úložiště, který jste vytvořili.
7. V **služby** klikněte na tlačítko **objekty BLOB**. A **podokně služby Blob** otevře.
8. Klikněte na tlačítko **+ kontejner**.
9. V **název**, zadejte název kontejneru, například *b2c*. Tato hodnota se později označuje jako *containerName*.
9. Vyberte **Blob** jako **získat přístup k typu**. Klikněte na možnost **Vytvořit**.
10. Kontejner, který jste vytvořili se zobrazí v seznamu na **podokně služby Blob**.
11. Zavřít **objekty BLOB** podokně.
12. Na **podokno účtu úložiště**, klikněte na tlačítko **klíč** ikonu. **Podokno klíče Access** otevře.  
13. Poznamenejte si hodnotu **key1**. Tato hodnota se nazývá později *key1*.

## <a name="downloading-the-helper-tool"></a>Pomocný nástroj pro stažení

1.  Stáhněte si nástroj pomocné rutiny z [Githubu](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Uložit *B2C-službě Azure BLOB Storage-klienta master.zip* souboru na místním počítači.
3.  Extrahování obsahu souboru B2C-službě Azure BLOB Storage-klienta master.zip na místním disku, třeba v části **sady přizpůsobení uživatelského rozhraní** složky, která vytvoří *B2C-službě Azure BLOB Storage – klient master*složky pod.
4.  Otevřete složku a extrahování obsahu souboru archivu *B2CAzureStorageClient.zip* v něm.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Nahrání ukázkových souborů sady přizpůsobení uživatelského rozhraní

1.  Pomocí Průzkumníka Windows přejděte do složky *B2C-službě Azure BLOB Storage – klient master* umístěna ve složce *sady přizpůsobení uživatelského rozhraní* složky vytvořené v předchozí části.
2.  Spustit *B2CAzureStorageClient.exe* souboru. Tento program nahraje všechny soubory v adresáři, zadejte do vašeho účtu úložiště a povolení CORS přístup k těmto souborům.
3.  Po zobrazení výzvy zadejte:.  Název účtu úložiště *storageAccountName*, například *contoso369b2c*.
    b.  Primární přístupový klíč azure blob storage, *key1*, například *contoso369b2c*.
    c.  Název kontejneru úložiště objektů blob storage, *containerName*, například *b2c*.
    d.  Cesta *Starter Pack* ukázkové soubory, například *... \B2CTemplates\wingtiptoys*.

Pokud jste postupovali podle předchozích kroků, HTML5 a CSS soubory *sady přizpůsobení uživatelského rozhraní* pro fiktivní společnost **Northwind** nyní odkazuje na vašem účtu úložiště.  Můžete ověřit, že obsah je nahraná správně tak, že otevřete podokno související kontejner na webu Azure Portal. Můžete také ověřit, že obsah je nahraná správně přístupem na stránku v prohlížeči. Další informace najdete v tématu [Azure Active Directory B2C: pomocným nástrojem použitá k předvedení stránce uživatelského rozhraní (UI) přizpůsobení funkce](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Ujistěte se, že účet úložiště má zapnuté CORS

CORS (sdílení prostředků různého původu) musí být povolené na váš koncový bod pro Azure AD B2C k načtení obsahu. Je to proto, že váš obsah je hostované v jiné doméně než doména, kterou Azure AD B2C se obsluhují ze stránky.

Ověřte, že úložiště, které hostují váš obsah na má zapnuté CORS, postupujte podle následujících kroků:

1. Otevřete relaci procházení a přejděte na stránku *unified.html* úplnou adresu URL umístění ve vašem účtu úložiště pomocí `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Například, https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Přejděte na adresu https://test-cors.org. Tento web umožňuje ověřit, že stránka, kterou používáte má zapnuté CORS.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. V **adresa URL vzdáleného úložiště**, zadejte úplnou adresu URL pro váš obsah unified.html a klikněte na tlačítko **odeslat požadavek**.
4. Ověřte, že výstup v **výsledky** oddíl obsahuje *XHR stav: 200*, což znamená, že je povoleno CORS.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
Účet úložiště by měl nyní obsahovat kontejner objektů blob s názvem *b2c* obrázku, který obsahuje následující Northwind šablony z *Starter Pack*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

Následující tabulka popisuje účel předchozí stránky HTML5.

| Šablona HTML5 | Popis |
|----------------|-------------|
| *phonefactor.html* | Na této stránce lze použít jako šablonu pro stránku služby Multi-Factor authentication. |
| *resetpassword.html* | Tato stránka slouží jako šablona pro stránka zapomenuté heslo. |
| *selfasserted.html* | Tato stránka slouží jako šablona pro účet na sociální síti zaregistrujte stránku, místní účet registrační stránku nebo místní účet přihlašovací stránku. |
| *unified.html* | Tato stránka slouží jako šablona pro jednotné přihlášení nebo přihlašovací stránky. |
| *updateprofile.html* | Tato stránka slouží jako šablona pro stránku aktualizace profilu. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Přidání odkazu do šablon HTML5/CSS pro cestu uživatele

Přímou úpravou vlastní zásady můžete svou cestu uživatele přidat odkaz do šablon HTML5 a CSS.

Vlastní šablony HTML5/CSS pro použití na vaší cestě uživatele musí být uvedený v seznamu obsahu definic, které lze použít v těchto cest uživatele. Pro tento účel, volitelně *<ContentDefinitions>* elementu XML musí být deklarována v rámci *<BuildingBlocks>* část souboru XML vlastní zásady.

Následující tabulka popisuje sadu obsahu ID definic uznané identity Azure AD B2C prostředí modulu a typu stránky, která má vztah k nim.

| ID obsahu definice | Popis |
|-----------------------|-------------|
| *api.error* | **Chybová stránka**. Na této stránce se zobrazí, když došlo k výjimce nebo došlo k chybě. |
| *API.idpselections* | **Stránka výběru zprostředkovatele identit**. Tato stránka obsahuje seznam zprostředkovatelů identity, které může uživatel vybrat při přihlašování. Zprostředkovatelé jsou enterprise zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook nebo Google + nebo místní účty (podle e-mailové adresy nebo uživatelského jména). |
| *api.idpselections.signup* | **Výběr zprostředkovatele identity pro registraci**. Tato stránka obsahuje seznam zprostředkovatelů identity, které může uživatel vybírat během registrace. Zprostředkovatelé jsou enterprise zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook nebo Google + nebo místní účty (podle e-mailové adresy nebo uživatelského jména). |
| *api.localaccountpasswordreset* | **Stránka zapomenuté heslo**. Tato stránka obsahuje formulář, který má uživatel k vyplnění zahájíte resetování hesla.  |
| *API.localaccountsignin* | **Místní účet přihlašovací stránku**. Tato stránka obsahuje přihlašovací formulář, který má uživatel k vyplnění při přihlašování pomocí místní účet, který je založen na e-mailovou adresu nebo jméno uživatele. Formulář může obsahovat pole textového zadání a pole pro zadání hesla. |
| *API.localaccountsignup* | **Stránku pro přihlášení místním účtem**. Tato stránka obsahuje registrace formulář, který má uživatel k vyplnění při přihlašování k odběru pro místní účet, který je založen na e-mailovou adresu nebo jméno uživatele. Formulář může obsahovat různé vstupní ovládací prvky, jako je například pole textového zadání, pole pro zadání hesla, přepínač, jedním výběrem rozevírací seznamy a vyberte zaškrtávací políčka. |
| *api.phonefactor* | **Stránka služby Multi-Factor authentication**. Na této stránce si uživatelé mohli ověřit jejich telefonních čísel (pomocí textových nebo hlasových) během registrace nebo přihlášení. |
| *api.selfasserted* | **Stránka registrace sociálního účtu**. Tato stránka obsahuje registrace formulář, který má uživatel k vyplnění při přihlašování k odběru pomocí existujícího účtu ze zprostředkovatele sociální identity, jako je Facebook nebo Google +. Tato stránka je podobný jako předchozí stránka registrace sociálního účtu s výjimkou pole pro zadání hesla. |
| *api.selfasserted.profileupdate* | **Stránka pro aktualizaci profilu**. Tato stránka obsahuje formulář, který může uživatel použít k aktualizaci jejich profilů. Tato stránka je podobný jako předchozí stránka registrace sociálního účtu s výjimkou pole pro zadání hesla. |
| *api.signuporsignin* | **Jednotná stránka registrace nebo přihlašování**.  Tato stránka zpracuje obě registrace a přihlášení uživatelů, kteří můžou využívat enterprise zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook nebo Google + nebo místním účtům.

## <a name="next-steps"></a>Další postup
[Referenční dokumentace: Pochopit, jak vlastní zásady fungují s architekturu rozhraní identit v B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
