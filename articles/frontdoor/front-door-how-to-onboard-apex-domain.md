---
title: Připojit kořenovou nebo vrcholovou doménu k existujícímu frontě Azure Portal
description: Naučte se, jak připojit kořenovou nebo vrcholovou doménu k existujícím předním dveřím pomocí Azure Portal.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 11/13/2020
ms.author: duau
ms.openlocfilehash: 55eefe7a7490df050aa7ebc2bb41fbadcc8d8279
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94646334"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Onboarding kořenové nebo vrcholové domény ve službě Front Door
Přední dveře Azure pomocí záznamů CNAME ověří vlastnictví domény pro připojování vlastních domén. Přední dveře nevystavují IP adresu front-endu přidruženou k profilu front-dveří. Proto nemůžete mapovat svoji doménu vrcholu na IP adresu, pokud je vaším záměrem připojit se k Azure na přední dveře.

Protokol DNS brání přiřazení záznamů CNAME ve vrcholu zóny. Pokud je vaše doména například, `contoso.com` můžete vytvořit záznamy CNAME pro `somelabel.contoso.com` , ale nemůžete vytvořit záznam CNAME pro `contoso.com` sebe samé. Toto omezení představuje problém pro vlastníky aplikace, kteří mají aplikace s vyrovnáváním zatížení za předními dveřmi Azure. Vzhledem k tomu, že použití profilu front-dveří vyžaduje vytvoření záznamu CNAME, není možné nasměrovat na přední profil od vrcholu zóny.

Tento problém lze vyřešit pomocí záznamů aliasů v Azure DNS. Na rozdíl od záznamů CNAME se záznamy aliasů vytvářejí ve vrcholu zóny. Vlastníci aplikace ji můžou použít k ukázání záznamu vrcholu zóny na frontu, který má veřejné koncové body. Vlastníci aplikace odkazují na stejný profil front-dveří, který se používá pro všechny ostatní domény v rámci zóny DNS. Například `contoso.com` a `www.contoso.com` může odkazovat na stejný profil front-dveří. 

Mapování vrcholu nebo kořenové domény na profil předních dveří v podstatě vyžaduje sloučení záznamů CNAME nebo dohledávání DNS. Mechanismus, ve kterém poskytovatel DNS rekurzivně vyřeší záznam CNAME, dokud nezíská IP adresu. Tato funkce je podporovaná Azure DNS pro koncové body front-dveří. 

> [!NOTE]
> K dispozici jsou i další poskytovatelé DNS, kteří podporují sloučení záznamů CNAME nebo DNS dohledávání, ale u svých zákazníků doporučujeme používat Azure DNS pro své zákazníky k hostování svých domén.

Můžete použít Azure Portal k zaregistrování domény vrcholu na vašich frontách a povolit na ní HTTPS tím, že ji přidružíte k certifikátu pro ukončení protokolu TLS. Domény vrcholu se také označují jako kořenové nebo holé domény.

## <a name="create-an-alias-record-for-zone-apex"></a>Vytvoří záznam aliasu pro vrchol zóny.

1. Otevřete konfiguraci **Azure DNS** pro doménu, která se má připojit.

1. Vytvoří nebo upraví záznam pro vrchol zóny.

1. Vyberte **typ** *záznamu jako záznam* a pak pro **sadu záznamů aliasů** vyberte *Ano* . **Typ aliasu** by měl být nastaven na *prostředek Azure*.

1. Vyberte předplatné Azure, ve kterém se má profil front-dveří hostovat. Pak vyberte prostředek front-dveří z rozevíracího seznamu **prostředků Azure** .

1. Kliknutím na **tlačítko OK** odešlete změny.

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="Záznam aliasu pro vrchol zóny":::

1. Výše uvedený krok vytvoří záznam vrcholu zóny ukazující na prostředek front-dveří a také mapování záznamů CNAME "afdverify" (příklad- `afdverify.contosonews.com` ) na to, které se bude používat pro připojování domény do profilu front-dveří.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Zprovoznění vlastní domény na předních dveřích

1. Na kartě přední Návrhář dvířek vyberte v části hostitelé se front-endu ikonu ' + ' a přidejte novou vlastní doménu.

1. Do pole název vlastního hostitele zadejte název kořenové nebo vrcholové domény, například `contosonews.com` .

1. Jakmile se ověří mapování CNAME z domény na vaše přední dvířka, vyberte v části **Přidat** vlastní doménu.

1. Vyberte **Uložit** a odešlete změny.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="Nabídka Vlastní domény":::

## <a name="enable-https-on-your-custom-domain"></a>Povolení HTTPS pro vlastní doménu

1. Vyberte vlastní doménu, kterou jste přidali, a v části **vlastní doména https** změňte stav na **povoleno**.

1. Vyberte  **typ správy certifikátů** použít vlastní *certifikát*.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="Nastavení HTTPS vlastní domény":::    

   > [!WARNING]
   > Typ správy certifikátů na předních dveřích se v současné době pro vrcholy nebo kořenové domény nepodporuje. Jediná možnost, která je k dispozici pro povolení protokolu HTTPS ve vrcholu nebo kořenové doméně pro přední dveře, používá vlastní certifikát TLS/SSL hostovaný na Azure Key Vault.

1. Než přejdete k dalšímu kroku, ujistěte se, že jste nastavili správná oprávnění pro přední dveře pro přístup k trezoru klíčů, jak je uvedeno v uživatelském rozhraní.

1. Zvolte **účet Key Vault** z aktuálního předplatného a pak vyberte příslušnou **tajnou** a **tajnou verzi** , která se namapuje na správný certifikát.

1. Výběrem **aktualizovat** uložte výběr a pak vyberte **Uložit**.

1. Po několika minutách vyberte **obnovit** a potom znovu vyberte vlastní doménu, abyste viděli, jak se zřizování certifikátů bude zobrazovat. 

> [!WARNING]
> Ujistěte se, že jste vytvořili odpovídající pravidla směrování pro vaši doménu vrcholů nebo jste přidali doménu do stávajících pravidel směrování.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
