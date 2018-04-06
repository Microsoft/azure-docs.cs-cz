---
title: Přizpůsobení uživatelského rozhraní cesty uživatele pomocí vlastních zásad | Microsoft Docs
description: Další informace o vlastních zásad Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/25/2017
ms.author: davidmu
ms.openlocfilehash: 4fe9e90996c56773480eb147e5aef7475453fe43
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Přizpůsobení uživatelského rozhraní cesty uživatele pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Tento článek slouží k pokročilé popis fungování přizpůsobení uživatelského rozhraní a povolení pomocí vlastních zásad Azure AD B2C, pomocí rozhraní prostředí Identity.


Integrované uživatelské prostředí je klíč pro řešení business k příjemce. Integrované uživatelské prostředí je prostředí, na zařízení nebo prohlížeče, kde je lišit od zákazníků služby, kterou používají uživatelské cesty přes službu.

## <a name="understand-the-cors-way-for-ui-customization"></a>Pochopení způsobu CORS pro přizpůsobení uživatelského rozhraní

Azure AD B2C umožňuje přizpůsobení vzhledu a chování činnost koncového uživatele (UX) na různých stránkách, které jsou zpracovat a zobrazit pomocí Azure AD B2C, používat vlastní zásady.

K tomuto účelu, Azure AD B2C spuštěním kódu v prohlížeči vaše příjemce a používá moderní a standard přístup [sdílení prostředků různých původů (CORS)](http://www.w3.org/TR/cors/) se načíst vlastní obsah z konkrétní adresy URL, kterou zadáte v vlastní zásady tak, aby odkazoval na vaše šablony HTML5 nebo šablon stylů CSS. CORS je mechanismus, který umožňuje omezené prostředky, jako je písem a na webové stránce budou vyžádány z jiné domény mimo doménu, z něhož pochází prostředku.

Ve srovnání s původním tradičním způsobem, jakým, kde jsou vlastní šablony stránky řešení, které jste zadali omezené textu a obrázků, tam, kde omezené řízení rozložení a chování byla nabízí vedoucí k více než problémy zajistit uživatelům prostředí, tak CORS podporuje HTML5 a šablon stylů CSS a umožňují:

- Hostování daného obsahu a řešení vloží jeho ovládacích prvků pomocí skriptu na straně klienta.
- Máte plnou kontrolu nad každý pixel rozložení a chování.

Můžete zadat libovolný počet obsahu stránek jako tím, že vytvoří soubory HTML5 nebo šablon stylů CSS podle potřeby.

> [!NOTE]
> Z bezpečnostních důvodů je používání jazyka JavaScript aktuálně blokován pro přizpůsobení. Chcete-li odblokovat JavaScript, je potřeba použít vlastní název domény pro vašeho tenanta Azure AD B2C.

V každé z vašich šablon HTML5 nebo šablon stylů CSS je zadat *ukotvení* element, který odpovídá požadovaným `<div id=”api”>` elementu v kódu HTML nebo jako znázornění dále stránky obsahu. Azure AD B2C vyžaduje, aby měly všechny stránky obsahu této konkrétní div.

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

Azure AD B2C související obsah stránky je vsunout do této div, když je váš zbývající části stránky na ovládací prvek. Kód jazyka JavaScript Azure AD B2C vrátí součástí obsahu a vloží do této konkrétní div elementu HTML. Azure AD B2C vloží následující ovládací prvky podle potřeby: účet ovládacího prvku výběru, přihlaste se ovládací prvky, Multi-Factor (aktuálně založené na telefonu) a ovládací prvky kolekce atributů. Azure AD B2C zajišťuje, že všechny ovládacích prvků HTML5 kompatibilní a přístupné, může být všechny ovládací prvky ve plně, a zda není vrátit verze ovládacího prvku.

Nakonec se zobrazí sloučené obsah dynamické dokumentu, který má vaše příjemce.

Chcete-li zajistit, že vše funguje podle očekávání, postupujte takto:

- Zajistěte, aby byl váš obsah HTML5 kompatibilní a dostupné
- Zajistěte, aby že vaše servery obsahu je povolený pro CORS.
- Poskytovat obsah přes protokol HTTPS.
- Například používat absolutní adresy URL https://yourdomain/content pro všechny odkazy a obsah šablon stylů CSS.

> [!TIP]
> Ověřte, zda jsou hostování obsahu na lokalitu má povolení CORS a testování požadavků CORS, můžete použít webu http://test-cors.org/. Díky této lokalitě můžete odeslat požadavek CORS na vzdálený server (abyste zjistili, zda je podporován CORS) nebo odeslat požadavek CORS na testovací server (Chcete-li prozkoumat určité funkce CORS).

> [!TIP]
> Lokality http://enable-cors.org/ také se považuje za více než užitečné zdroje na CORS.

Díky tohoto přístupu na základě CORS koncoví uživatelé měli konzistentního prostředí mezi vaší aplikace a stránky obsluhuje Azure AD B2C.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Předpokladem je musíte vytvořit účet úložiště. Potřebujete předplatné Azure k vytvoření účtu Azure Blob Storage. Můžete si zaregistrovat bezplatnou zkušební verzi na [webu Azure](https://azure.microsoft.com/pricing/free-trial/).

1. Spustí relaci prohlížeče a přejděte do [portál Azure](https://portal.azure.com).
2. Přihlaste se pomocí přihlašovacích údajů správce.
3. Klikněte na tlačítko **vytvořit prostředek** > **úložiště** > **účet úložiště**.  A **vytvořit účet úložiště** podokně otevře.
4. V **název**, zadejte název pro účet úložiště, například *contoso369b2c*. Tato hodnota se později označuje jako *storageAccountName*.
5. Vyberte odpovídající možnosti pro cenové úrovně, skupiny prostředků a odběr. Ujistěte se, že máte **připnout na úvodní panel** možnost zaškrtnutí. Klikněte na možnost **Vytvořit**.
6. Přejděte zpět na úvodní panel a klikněte na účet úložiště, který jste vytvořili.
7. V **služby** klikněte na tlačítko **objekty BLOB**. A **podokně služby objektů Blob** otevře.
8. Klikněte na tlačítko **+ kontejner**.
9. V **název**, zadejte název kontejneru, například *b2c*. Tato hodnota se později označuje jako *containerName*.
9. Vyberte **Blob** jako **přistupovat typu**. Klikněte na možnost **Vytvořit**.
10. Kontejner, který jste vytvořili se zobrazí v seznamu na **podokně služby objektů Blob**.
11. Zavřít **objekty BLOB** podokně.
12. Na **podokně účet úložiště**, klikněte na tlačítko **klíč** ikonu. **Přístup klíče podokně** otevře.  
13. Poznamenejte si hodnotu **key1**. Tato hodnota se nazývá později *key1*.

## <a name="downloading-the-helper-tool"></a>Stažení nástroje pomocné rutiny

1.  Stáhněte si nástroj pomocné rutiny z [Githubu](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Uložit *B2C-AzureBlobStorage-klienta master.zip* soubor na místním počítači.
3.  Rozbalte obsah souboru B2C-AzureBlobStorage-klienta master.zip na váš místní disk, například v části **sady přizpůsobení uživatelského rozhraní** složky, která vytvoří *B2C-AzureBlobStorage-klienta master*složky pod.
4.  Otevřete tuto složku a extrahování obsahu souboru archivu *B2CAzureStorageClient.zip* v něm.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Odeslání ukázkových souborů sady přizpůsobení uživatelského rozhraní

1.  Pomocí Průzkumníka Windows přejděte do složky *B2C AzureBlobStorage klienta hlavní* umístěná *sady přizpůsobení uživatelského rozhraní* složky vytvořené v předchozí části.
2.  Spustit *B2CAzureStorageClient.exe* souboru. Tento program ukládání všechny soubory v adresáři, zadejte na váš účet úložiště a povolení CORS přístup pro tyto soubory.
3.  Po zobrazení výzvy zadejte:.  Název účtu úložiště *storageAccountName*, například *contoso369b2c*.
    b.  Primární přístupový klíč úložiště objektů blob v azure, *key1*, například *contoso369b2c*.
    c.  Název vašeho kontejneru úložiště objektů blob úložiště *containerName*, například *b2c*.
    d.  Cestu *Starter-Pack* ukázkové soubory, například *... \B2CTemplates\wingtiptoys*.

Pokud jste postupovali podle předchozích kroků, soubory jazyka HTML5 a šablon stylů CSS *sady přizpůsobení uživatelského rozhraní* pro fiktivní společnost **Northwind** nyní ukazovat na účtu úložiště.  Můžete ověřit, že obsah se nahrají správně otevřením podokna související kontejneru na portálu Azure. Případně můžete ověřit, obsah se nahrají správně přístupem na stránku v prohlížeči. Další informace najdete v tématu [Azure Active Directory B2C: pomocným nástrojem použitá k předvedení stránce uživatelské rozhraní (UI) přizpůsobení funkce](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Zajistěte, aby byl účet úložiště povolení CORS

CORS (sdílení prostředků různého původu) musí být povolené na váš koncový bod pro Azure AD B2C načíst obsah. Je to proto, že obsah je hostovaná v jiné doméně než doménu, kterou Azure AD B2C bude obsluhující stránku z.

Ověřte, že úložiště, které jsou hostiteli obsahu na má povolení CORS, pokračujte následující kroky:

1. Spustí relaci prohlížeče a přejděte na stránku *unified.html* pomocí úplnou adresu URL jeho umístění ve vašem účtu úložiště `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Například, https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Přejděte na adresu http://test-cors.org. Tento web můžete ověřit, zda stránka, kterou používáte má povolení CORS.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. V **vzdálenou adresou URL**, zadejte úplnou adresu URL pro váš obsah unified.html a klikněte na tlačítko **odeslat požadavek**.
4. Ověřte, že výstup v **výsledky** část obsahuje *XHR stav: 200*, což naznačuje, že je povolena CORS.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
Účet úložiště by měl nyní obsahovat kontejner objektů blob s názvem *b2c* na obrázku, který obsahuje následující Northwind šablony z *Starter-Pack*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

Následující tabulka popisuje účel předchozí stránkách HTML5.

| HTML5 šablony | Popis |
|----------------|-------------|
| *phonefactor.html* | Tato stránka slouží jako šablona pro stránku služby Multi-Factor authentication. |
| *resetpassword.html* | Tato stránka slouží jako šablona pro zapomněli jste heslo?. |
| *selfasserted.html* | Tato stránka slouží jako šablonu pro účet sociálních zaregistrovat stránky, na místní účet přihlašovací stránku nebo na přihlašovací stránku místní účet. |
| *unified.html* | Tato stránka slouží jako šablona pro jednotné registrace nebo přihlášení. |
| *updateprofile.html* | Tato stránka slouží jako šablona pro stránku aktualizace profilu. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Přidat odkaz na vaše šablony HTML5 nebo šablon stylů CSS pro vaše uživatele cesty

Přímou úpravou vlastní zásady můžete vám dobře slouží uživatele přidáte propojení na vaše šablony HTML5 nebo šablon stylů CSS.

Vlastní šablony HTML5 nebo šablon stylů CSS pro použití v vám dobře slouží uživatele muset zadat seznam obsahu definice, které lze použít v těchto cesty uživatele. K tomuto účelu, volitelný *<ContentDefinitions>* – element XML musí být deklarován v části *<BuildingBlocks>* části souboru XML vlastní zásady.

Následující tabulka popisuje sadu ID definice rozpoznáno identit Azure AD B2C prostředí stroj a typ stránky, která má vztah k jejich obsah.

| ID obsahu definice | Popis |
|-----------------------|-------------|
| *api.error* | **Chybové stránky**. Tato stránka se zobrazí, když je došlo k výjimce nebo došlo k chybě. |
| *api.idpselections* | **Stránka Výběr zprostředkovatele identity**. Tato stránka obsahuje seznam zprostředkovatelů identity, které může uživatel vybírat během přihlášení. Tyto zprostředkovatele jsou poskytovatelů identit enterprise, poskytovatelů identit sociálních třeba Facebook a Google + nebo místní účty (podle e-mailové adresy nebo uživatelské jméno). |
| *api.idpselections.signup* | **Výběr zprostředkovatele identity pro registraci**. Tato stránka obsahuje seznam poskytovatelů identity, které může uživatel vybírat během registrace. Tyto zprostředkovatele jsou poskytovatelů identit enterprise, poskytovatelů identit sociálních třeba Facebook a Google + nebo místní účty (podle e-mailové adresy nebo uživatelské jméno). |
| *api.localaccountpasswordreset* | **Zapomněli jste heslo**. Tato stránka obsahuje formulář, který má uživatel k vyplnění zahájíte jejich resetování hesla.  |
| *api.localaccountsignin* | **Přihlašovací stránka místní účet**. Tato stránka obsahuje formulář přihlášení, který má uživatel vyplnit při přihlášení pomocí místního účtu, který je založený na e-mailovou adresu nebo uživatelské jméno. Formulář může obsahovat vstupní textové pole a pole pro zadání hesla. |
| *api.localaccountsignup* | **Místní účet stránku**. Tato stránka obsahuje registrační formulář, který má uživatel vyplnit při registraci pro místní účet, který je založený na e-mailovou adresu nebo uživatelské jméno. Formulář může obsahovat různé vstupní ovládací prvky jako vstupní textové pole, pole pro zadání hesla, přepínač, polí rozevíracího seznamu vyberte jeden a více vyberte zaškrtávací políčka. |
| *api.phonefactor* | **Stránka služby Multi-Factor authentication**. Na této stránce si uživatelé mohli ověřit jejich telefonních čísel (pomocí textové nebo hlasové) během registrace nebo přihlášení. |
| *api.selfasserted* | **Stránku pro přihlášení sociálních účet**. Tato stránka obsahuje registrační formulář, který má uživatel vyplnit při registraci pomocí existujícího účtu od poskytovatele identity sociálních třeba Facebook nebo Google +. Tato stránka je podobný na předchozí stránku registrace sociálních účtu s výjimkou pole pro zadání hesla. |
| *api.selfasserted.profileupdate* | **Stránka pro aktualizaci profilu**. Tato stránka obsahuje formulář, který uživatel může použít k aktualizaci svůj profil. Tato stránka je podobný na předchozí stránku registrace sociálních účtu s výjimkou pole pro zadání hesla. |
| *api.signuporsignin* | **Jednotná stránku registrace nebo přihlášení**.  Tato stránka zpracovává obě registrace a přihlášení uživatelů, kteří můžou využívat poskytovatelů identit enterprise, poskytovatelů identit sociálních třeba Facebook nebo Google + nebo místní účty.

## <a name="next-steps"></a>Další postup
[Referenční dokumentace: Pochopit, jak vlastních zásad práce s rozhraní Identity prostředí v B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
