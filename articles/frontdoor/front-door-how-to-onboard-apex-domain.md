---
title: Připojit kořenovou nebo vrcholovou doménu k existujícímu frontě Azure Portal
description: Naučte se, jak připojit kořenovou nebo vrcholovou doménu k existujícím předním dveřím pomocí Azure Portal.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: duau
ms.openlocfilehash: 05267ad43f6e7f89ec50b1765d2475a02fae1702
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399568"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Onboarding kořenové nebo vrcholové domény ve službě Front Door
Přední dveře Azure pomocí záznamů CNAME ověří vlastnictví domény pro připojování vlastních domén. Přední dveře také nezveřejňují front-end IP adresu přidruženou k profilu front-endu, takže nemůžete mapovat svou doménu vrcholu na IP adresu, pokud je cílem připojit se do front-endu Azure.

Protokol DNS brání přiřazení záznamů CNAME ve vrcholu zóny. Pokud je vaše doména například, `contoso.com` můžete vytvořit záznamy CNAME pro `somelabel.contoso.com` , ale nemůžete vytvořit záznam CNAME pro `contoso.com` sebe samé. Toto omezení představuje problém pro vlastníky aplikace, kteří mají aplikace s vyrovnáváním zatížení za předními dveřmi Azure. Vzhledem k tomu, že použití profilu front-dveří vyžaduje vytvoření záznamu CNAME, není možné nasměrovat na přední profil od vrcholu zóny.

Tento problém je vyřešen pomocí záznamů aliasů v Azure DNS. Na rozdíl od záznamů CNAME se záznamy aliasů vytvoří ve vrcholu zóny a vlastníci aplikace je můžou použít k tomu, aby odkazovaly na svůj záznam vrcholu zóny na profil front dveří, který má veřejné koncové body. Vlastníci aplikace odkazují na stejný profil front-dveří, který se používá pro všechny ostatní domény v rámci zóny DNS. Například `contoso.com` a `www.contoso.com` může odkazovat na stejný profil front-dveří. 

Mapování vrcholu nebo kořenové domény na profil předních dveří v podstatě vyžaduje sloučení záznamů CNAME nebo dohledávání DNS, což je mechanismus, ve kterém zprostředkovatel DNS rekurzivně překládá záznam CNAME, dokud nezíská IP adresu. Tato funkce je podporovaná Azure DNS pro koncové body front-dveří. 

> [!NOTE]
> K dispozici jsou i další poskytovatelé DNS, kteří podporují sloučení záznamů CNAME nebo DNS dohledávání, ale u svých zákazníků doporučujeme používat Azure DNS pro své zákazníky k hostování svých domén.

Můžete použít Azure Portal k zaregistrování domény vrcholu na vašich frontách a povolit na ní HTTPS tím, že ji přidružíte k certifikátu pro ukončení protokolu TLS. Domény vrcholu se také označují jako kořenové nebo holé domény.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření záznamu aliasu, který odkazuje na profil front-dveří
> * Přidání kořenové domény do front-dveří
> * Nastavení HTTPS v kořenové doméně

> [!NOTE]
> Tento kurz vyžaduje, abyste už vytvořili profil front-dveří. Projděte si další kurzy, jako je [rychlý Start: vytvořením přední dveře](./quickstart-create-front-door.md) nebo [vytvořením front-bran a pomocí přesměrování HTTP na https](./front-door-how-to-redirect-https.md) můžete začít.

## <a name="create-an-alias-record-for-zone-apex"></a>Vytvoří záznam aliasu pro vrchol zóny.

1. Otevřete konfiguraci **Azure DNS** pro doménu, která se má připojit.
2. Vytvoří nebo upraví záznam pro vrchol zóny.
3. Vyberte **typ** _záznamu jako záznam_ a pak pro **sadu záznamů aliasů**vyberte _Ano_ . **Typ aliasu** by měl být nastaven na _prostředek Azure_.
4. Zvolte předplatné Azure, ve kterém je profil front-dveří hostovaný, a pak vyberte prostředek front-dveří z rozevíracího seznamu **prostředků Azure** .
5. Změny odešlete kliknutím na tlačítko **OK** .

    ![Záznam aliasu pro vrchol zóny](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. Výše uvedený krok vytvoří záznam vrcholu zóny ukazující na prostředek front-dveří a také mapování záznamů CNAME "afdverify" (příklad- `afdverify.contosonews.com` ), na `afdverify.<name>.azurefd.net` které se bude používat pro připojování domény do profilu front-dveří.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Zprovoznění vlastní domény na předních dveřích

1. Na kartě přední Návrhář dvířek klikněte na ikonu ' + ' v části hostitelé front-endu a přidejte novou vlastní doménu.
2. Do pole název vlastního hostitele zadejte název kořenové nebo vrcholové domény, například `contosonews.com` .
3. Po ověření mapování CNAME z domény na vaše přední dvířka klikněte na **Přidat** a přidejte vlastní doménu.
4. Změny odešlete kliknutím na **Uložit** .

![Nabídka Vlastní domény](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Povolení HTTPS pro vlastní doménu

1. Klikněte na vlastní doménu, kterou jste přidali, a v části **vlastní doména https**změňte stav na **povoleno**.
2. Vyberte  **typ správy certifikátů** použít vlastní _certifikát_.

> [!WARNING]
> Typ správy certifikátů na předních dveřích se v současné době pro vrcholy nebo kořenové domény nepodporuje. Jediná možnost, která je k dispozici pro povolení protokolu HTTPS ve vrcholu nebo kořenové doméně pro přední dveře, používá vlastní certifikát TLS/SSL hostovaný na Azure Key Vault.

3. Než přejdete k dalšímu kroku, ujistěte se, že jste nastavili správná oprávnění pro přední dveře pro přístup k trezoru klíčů, jak je uvedeno v uživatelském rozhraní.
4. Zvolte **účet Key Vault** z aktuálního předplatného a pak vyberte příslušnou **tajnou** a **tajnou verzi** , která se namapuje na správný certifikát.
5. Kliknutím na **aktualizovat** uložte výběr a pak klikněte na **Uložit**.
6. Po několika minutách klikněte na **aktualizovat** a pak znovu klikněte na vlastní doménu. zobrazí se průběh zřizování certifikátu. 

> [!WARNING]
> Ujistěte se, že jste vytvořili odpovídající pravidla směrování pro vaši doménu vrcholů nebo jste přidali doménu do stávajících pravidel směrování.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).