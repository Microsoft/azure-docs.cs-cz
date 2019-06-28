---
title: Připojení z kořenové nebo vrcholu domény do existující branou pomocí webu Azure portal
description: Zjistěte, jak chcete připojit z kořenové nebo vrcholu domény do existující branou pomocí webu Azure portal.
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 464c38b0ece274d2ea7df89ab9fd7c0a60dc4b58
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332889"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Připojení z kořenové nebo vrcholu domény na přední dveře
Záznamy CNAME branou Azure používá k ověření vlastnictví domény pro připojení vlastních domén. Také branou nezveřejňuje IP adresu front-endu, který je přidružený k profilu branou, a proto nelze mapovat vaši doménu vrcholu na IP adresu, pokud je cílem připojit se k Azure branou.

Protokol DNS brání přiřazení CNAME záznamů ve vrcholu zóny. Například, pokud je vaše doména `contoso.com`; můžete vytvořte záznamy CNAME pro `somelabel.contoso.com`; ale nemůžete vytvořit záznam CNAME pro `contoso.com` samotný. Toto omezení představuje problém pro počet vlastníků aplikace, kteří mají s vyrovnáváním zatížení aplikací za branou Azure. Protože pomocí profilu branou vyžaduje vytvoření záznamu CNAME, není možné tak, aby odkazoval na profil branou z vrcholu zóny.

Tento problém je vyřešen pomocí záznamů aliasů v Azure DNS. Na rozdíl od záznamů CNAME alias záznamy jsou vytvářeny na vrcholu zóny a vlastníci aplikace můžete použít tak, aby odkazoval jejich záznamu vrcholu zóny branou profil, který má veřejné koncové body. Počet vlastníků aplikace odkazovat na stejný profil branou, který se používá u všech ostatních domén v rámci jejich zóny DNS. Například `contoso.com` a `www.contoso.com` může odkazovat na stejný profil branou. 

V podstatě mapování vrcholu nebo kořenové domény na váš profil branou vyžaduje sloučení CNAME nebo DNS dohledávání, což je mechanismus, kde v DNS poskytovatele rekurzivně řeší záznam CNAME dokud nedosáhne IP adresu. Tato funkce je podporována službou Azure DNS pro koncové body branou. 

> [!NOTE]
> Existují dalších poskytovatelů DNS a, které podporují CNAME sloučení nebo následné dotazy DNS, ale Azure branou doporučuje používat Azure DNS pro její zákazníky pro hostování své domény.

Můžete použít na webu Azure portal k začlenění doménu vrcholu na přední dveře a povolení HTTPS pro jeho přidružením s certifikátem pro ukončení protokolu SSL. Vrcholu domény jsou také označovány jako kořenový adresář nebo naked domén.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření záznamu o aliasu, který odkazuje na svůj profil branou
> * Přidat kořenovou doménu branou
> * Nastavení protokolu HTTPS v kořenové doméně

> [!NOTE]
> Tento kurz vyžaduje, že již máte profil branou vytvořili. Přečtěte si další kurzy jako [rychlý start: Vytvoření branou](./quickstart-create-front-door.md) nebo [vytvořit přední dveře k přesměrování protokolu HTTPS s protokolem HTTP](./front-door-how-to-redirect-https.md) začít.

## <a name="create-an-alias-record-for-zone-apex"></a>Vytvoření záznamu o aliasu pro vrcholu zóny

1. Otevřít **Azure DNS** konfigurace domény, který se má připojit.
2. Vytvořte nebo upravte záznam pro vrcholu zóny.
3. Vyberte záznam **typ** jako _A_ zaznamenávat a pak vyberte _Ano_ pro **sadu záznamů Alias**. **Typ aliasu** by mělo být nastavené _prostředků Azure_.
4. Zvolte předplatné Azure, kde se hostuje váš profil branou a pak vyberte prostředek branou z **prostředků Azure** rozevíracího seznamu.
5. Klikněte na tlačítko **OK** změny potvrďte.

    ![Alias record for zone apex](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. Výše uvedený krok vytvoří záznam vrcholu zóny odkazující na váš prostředek branou a také mapování záznamu CNAME "afdverify" (příklad – `afdverify.contosonews.com`) k `afdverify.<name>.azurefd.net` která bude použita pro registraci domény ve vašem profilu branou.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Připojit vlastní domény na přední dveře

1. Na kartě branou návrháře klikněte na '+' ikonu na hostitelích front-endu části přidáte novou vlastní doménu.
2. Zadejte název domény z kořenové nebo vrcholu v poli Název vlastního hostitele příklad `contosonews.com`.
3. Jakmile ověříte mapování CNAME z domény do vašeho branou, klikněte na **přidat** přidat vlastní doménu.
4. Klikněte na tlačítko **Uložit** odeslat provedené změny.

![Nabídka Vlastní domény](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Povolení HTTPS pro vlastní doménu

1. Klikněte na tlačítko pro vlastní doménu, která byla přidána a v části **HTTPS vlastní domény**, změňte stav na **povoleno**.
2. Vyberte **typ správy certifikátu** k _"Použít vlastní certifikát"_ .

> [!WARNING]
> Typ správy certifikátu přední dveře spravované aktuálně nepodporuje pro vrchol nebo kořenové domény. Jedinou možností, které jsou k dispozici pro povolení HTTPS pro doménu vrcholu nebo kořenového adresáře pro branou používá vlastní vlastního certifikátu SSL, které jsou hostované v Azure Key Vault.

3. Ujistěte se, že máte nastavení správná oprávnění pro vstupní brána k klíč trezoru, jak je uvedeno v uživatelském rozhraní, než budete pokračovat k dalšímu kroku.
4. Zvolte **účtu Key Vault** z aktuálního předplatného a pak vyberte příslušné **tajný kód** a **verzi tajného klíče** mapovat na správný certifikát.
5. Klikněte na **aktualizace** uložte výběr a potom klikněte na **Uložit**.
6. Klikněte na tlačítko **aktualizovat** za pár minut a pak klikněte na vlastní doménu znovu, abyste viděli průběh zřizování certifikátů. 

> [!WARNING]
> Ujistěte se, že máte vytvoří příslušná pravidla směrování pro vaši doménu vrcholu nebo přidat doménu do existující pravidla směrování.

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).