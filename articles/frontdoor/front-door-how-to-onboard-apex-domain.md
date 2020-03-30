---
title: Na palubě kořenové nebo vrcholové domény do existujícího portálu Front Door – Azure
description: Naučte se, jak napalubě kořenové nebo vrcholové domény k existujícím předním dveřím pomocí portálu Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: bb1042e15d4366923174996388eeb2fb99aef429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184618"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Na palubě kořenové nebo vrcholové domény na předních dveřích
Azure Front Door používá záznamy CNAME k ověření vlastnictví domény pro připojení vlastních domén. Front Door také nezveřejňuje ip adresu front-endu přidruženou k profilu front door a takže nemůžete mapovat doménu vrcholu na IP adresu, pokud je záměrem připojit ji k Azure Front Door.

Protokol DNS zabraňuje přiřazení záznamů CNAME na vrcholu zóny. Například pokud je `contoso.com`vaše doména ; můžete vytvořit záznamy CNAME pro `somelabel.contoso.com`; ale nemůžete vytvořit CNAME `contoso.com` pro sebe. Toto omezení představuje problém pro vlastníky aplikací, kteří mají aplikace s vyrovnáváním zatížení za Azure Front Door. Vzhledem k tomu, že použití profilu předních dveří vyžaduje vytvoření záznamu CNAME, není možné na profil předních dveří z vrcholu zóny ukázat.

Tento problém je vyřešen pomocí alias záznamů na Azure DNS. Na rozdíl od záznamů CNAME jsou záznamy aliasu vytvářeny v vrcholu zóny a vlastníci aplikací je mohou použít k navedení záznamu vrcholu zóny na profil Front Door, který má veřejné koncové body. Vlastníci aplikací odkazují na stejný profil front door, který se používá pro jakoukoli jinou doménu v rámci jejich zóny DNS. Například `contoso.com` a `www.contoso.com` může ukazovat na stejný profil předních dveří. 

Mapování vrcholu nebo kořenové domény na profil předních dveří v podstatě vyžaduje cname sloučení nebo dns chasing, což je mechanismus, kde poskytovatel DNS rekurzivně řeší položku CNAME, dokud nenarazí na IP adresu. Tato funkce je podporovaná koncovými body Azure DNS pro přední dveře. 

> [!NOTE]
> Existují i další poskytovatelé DNS, kteří podporují sloučení CNAME nebo honění DNS, ale Azure Front Door doporučuje používat Azure DNS pro své zákazníky pro hostování svých domén.

Portál Azure můžete použít k připojení domény vrcholu na předních dveřích a povolit na něm protokol HTTPS tím, že ho přisuzujete k certifikátu pro ukončení SSL. Domény Apex jsou také označovány jako kořenové nebo nahé domény.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření záznamu aliasu, který odkazuje na profil předních dveří
> * Přidání kořenové domény k předním dveřím
> * Nastavení protokolu HTTPS v kořenové doméně

> [!NOTE]
> Tento kurz vyžaduje, abyste již vytvořili profil předních dveří. Odkazovat další kurzy, jako [je úvodní příručka: Vytvořit přední dveře](./quickstart-create-front-door.md) nebo vytvořit přední dveře s http na https [přesměrování,](./front-door-how-to-redirect-https.md) abyste mohli začít.

## <a name="create-an-alias-record-for-zone-apex"></a>Vytvoření záznamu aliasu pro vrchol zóny

1. Otevřete konfiguraci **Azure DNS** pro doménu, která má být na palubě.
2. Vytvořte nebo upravte záznam pro vrchol zóny.
3. Vyberte **typ** záznamu jako _záznam A_ a pak vyberte _Ano_ pro sadu záznamů **alias .** **Typ aliasu** by měl být nastaven na _prostředek Azure_.
4. Zvolte předplatné Azure, kde je hostovaný profil předních dveří, a pak v rozevíracím seznamu **prostředků Azure** vyberte prostředek front door.
5. Chcete-li odeslat změny, klepněte na tlačítko **OK.**

    ![Aliasový záznam pro vrchol zóny](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. Výše uvedený krok vytvoří vrcholový záznam zóny směřující k vašemu prostředku předních dveří a `afdverify.contosonews.com`také `afdverify.<name>.azurefd.net` mapování záznamů CNAME "afdverify" (příklad - ), ke kterému bude použito pro připojení domény na vašem profilu předních dveří.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Na palubě vlastní domény na předních dveřích

1. Na kartě Návrhář předních dveří klikněte na ikonu +v části Frontend hosts a přidejte novou vlastní doménu.
2. Zadejte název kořenového adresáře nebo vrcholu domény `contosonews.com`do vlastního pole názvu hostitele, například .
3. Po ověření mapování CNAME z domény do předních dveří klikněte na **Přidat** a přidejte vlastní doménu.
4. Kliknutím na **Uložit** odešlete změny.

![Nabídka Vlastní domény](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Povolení protokolu HTTPS ve vlastní doméně

1. Klikněte na vlastní doménu, která byla přidána, a v části **Vlastní doména HTTPS**, změňte stav na **Povoleno**.
2. Vyberte **typ správy certifikátu** _pro použít vlastní certifikát_.

> [!WARNING]
> Typ správy spravovaného certifikátu Front Door není aktuálně podporován pro vrcholové nebo kořenové domény. Jedinou možností, která je k dispozici pro povolení protokolu HTTPS na vrcholu nebo kořenové doméně pro přední dveře, je použití vlastního certifikátu SSL hostovaného v trezoru klíčů Azure.

3. Než přejdete k dalšímu kroku, ujistěte se, že máte nastavena správná oprávnění pro přední dveře pro přístup k trezoru klíčů, jak je uvedeno v ui.
4. Zvolte **účet Trezoru klíčů** z aktuálního předplatného a pak vyberte příslušnou **tajnou** a **tajnou verzi,** kterou chcete namapovat na správný certifikát.
5. Kliknutím na **Aktualizovat** uložte výběr a klepněte na tlačítko **Uložit**.
6. Po několika minutách klikněte na **Aktualizovat** a potom znovu klikněte na vlastní doménu, abyste viděli průběh zřizování certifikátů. 

> [!WARNING]
> Ujistěte se, že jste pro doménu apex vytvořili příslušná pravidla směrování nebo jste ji přidali k existujícím pravidlům směrování.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).