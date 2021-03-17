---
title: Konfigurace HTTPS pro vlastní doménu v konfiguraci SKU služby Azure front-end Standard/Premium
description: V tomto článku se dozvíte, jak začlenit vlastní doménu do služby Azure front-SKU Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: c2edf11939996156c2b589b0b7876ae1b01466e5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740813"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Konfigurace HTTPS pro vlastní doménu front-SKU Standard/Premium (Preview) pomocí Azure Portal

> [!NOTE]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Služba Azure front bran Standard/Premium umožňuje ve výchozím nastavení zabezpečené doručování TLS do vašich aplikací, když se přidá vlastní doména. Pomocí protokolu HTTPS ve vaší vlastní doméně zajistíte zabezpečenou doručování citlivých dat pomocí šifrování TLS/SSL při posílání přes Internet. Když se webový prohlížeč připojí k webu přes HTTPS, ověří certifikát zabezpečení webu a to, že je vydán legitimní certifikační autoritou. Tento proces zajišťuje zabezpečení a chrání vaše webové aplikace před útoky.

Přední dveře Azure (Standard/Premium) podporují certifikát spravovaný službou Azure i certifikáty spravované zákazníkem. Přední dveře Azure ve výchozím nastavení automaticky povolí HTTPS všem vašim vlastním doménám pomocí spravovaných certifikátů Azure. Pro získání spravovaného certifikátu Azure nejsou nutné žádné další kroky. Certifikát se vytvoří během procesu ověřování domény. Můžete také použít vlastní certifikát integrací služby Azure front-end Standard/Premium s vaším Key Vault.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v tématu [**doplňujících podmínek použití pro Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="prerequisites"></a>Požadavky

* Než budete moct nakonfigurovat HTTPS pro vlastní doménu, musíte nejdřív vytvořit profil služby Azure front-Premium Standard/Premium. Další informace najdete v tématu [rychlý Start: vytvoření profilu služby Azure front-end Standard/Premium](create-front-door-portal.md).

* Pokud ještě nemáte vlastní doménu, musíte ji nejdřív koupit se zprostředkovatelem domény. Příklad najdete v tématu [Nákup vlastního názvu domény](../../app-service/manage-custom-dns-buy-domain.md).

* Pokud k hostování [domén DNS](../../dns/dns-overview.md)používáte Azure, musíte delegovat DNS (Domain Name System) poskytovatele domény na Azure DNS. Další informace najdete v tématu [delegování domény na Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). V opačném případě, pokud ke zpracování vaší domény DNS používáte poskytovatele domény, musíte doménu ověřit ručně tak, že zadáte výzvy záznamy TXT DNS.

## <a name="azure-managed-certificates"></a>Spravované certifikáty Azure

1. V části nastavení pro profil služby Azure front-end Standard/Premium vyberte **domény** a potom vyberte **+ Přidat** a přidejte novou doménu.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="Snímek obrazovky s cílovou stránkou konfigurace domény":::

1. Na stránce **Přidat doménu** pro *správu DNS* vyberte možnost **Azure Managed DNS** . 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Snímek obrazovky s vybraným přidáním stránky domény se službou Azure Managed DNS":::

1. Podle postupu v části povolení [vlastní domény](how-to-add-custom-domain.md)ověřte a přidružte vlastní doménu ke koncovému bodu.

1. Po úspěšném přiřazení vlastní domény ke koncovému bodu se spravovaný certifikát Azure nasadí do front-dveří. Dokončení tohoto procesu může trvat několik minut.

## <a name="using-your-own-certificate"></a>Používání vlastního certifikátu

Můžete se také rozhodnout použít vlastní certifikát TLS. Tento certifikát se musí naimportovat do Azure Key Vault předtím, než ho budete moct používat s Azure front-end Standard/Premium. Přečtěte si téma [Import certifikátu](../../key-vault/certificates/tutorial-import-certificate.md) do Azure Key Vault. 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Příprava účtu a certifikátu Azure Key Vault
 
1. Musíte mít spuštěný účet Azure Key Vault v rámci stejného předplatného, jako máte frontu Azure na úrovni Standard/Premium, u kterých chcete povolit vlastní HTTPS. Pokud účet Azure Key Vault nemáte, vytvořte ho.

    > [!WARNING]
    > Přední dveře Azure v současné době podporují jenom Key Vault účty ve stejném předplatném, jako je konfigurace front-dveří. Volba Key Vault v rámci jiného předplatného, než je vaše přední vrátka Azure na úrovni Standard/Premium, způsobí selhání.

1. Pokud už certifikát máte, můžete ho nahrát přímo na účet Azure Key Vault. V opačném případě vytvořte nový certifikát přímo prostřednictvím Azure Key Vault z jedné z partnerských certifikačních autorit, které Azure Key Vault integruje s. Nahrajte certifikát jako objekt **certifikátu** místo **tajného klíče**.

    > [!NOTE]
    > U vlastního certifikátu TLS/SSL nepodporuje přední dveře certifikáty s algoritmy kryptografie EC.

#### <a name="register-azure-front-door"></a>Registrovat přední dveře Azure

Zaregistrujte instanční objekt pro služby Azure front-dveří jako aplikaci ve vašem Azure Active Directory prostřednictvím PowerShellu.

> [!NOTE]
> Tato akce vyžaduje globální oprávnění správce a je nutné ji provést pouze **jednou** pro každého tenanta.

1. V případě potřeby nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) v PowerShellu na místním počítači.

1. V PowerShellu spusťte následující příkaz:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů pro Azure na přední dveře
 
Udělte pro přístup k certifikátům ve vašem účtu Azure Key Vault oprávnění pro přední dveře Azure.

1. V účtu trezoru klíčů v části nastavení vyberte **zásady přístupu**. Pak vyberte **Přidat nový** a vytvořte novou zásadu.

1. V **možnosti vybrat objekt zabezpečení** vyhledejte **205478c0-BD83-4e1b-a9d6-db63a3e1e1c8** a zvolte * * Microsoft. AzureFrontDoor-CDN * *. Klikněte na **Vybrat**.

1. V části **oprávnění tajného** kódu vyberte **získat** , pokud chcete, aby přední dvířka získala certifikát.

1. V části **oprávnění certifikátu** vyberte **získat** a umožněte tak, aby přední dvířka získala certifikát.

1. Vyberte **OK**. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Vyberte certifikát pro nasazení na přední dveře Azure.
 
1. Vraťte se do služby Azure front-in standard/Premium na portálu.

1. V části *Nastavení* přejděte na **tajná klíčová** pole a vyberte **Přidat certifikát**.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Snímek obrazovky s tajnou cílovou stránkou pro přední dveře Azure":::

1. Na stránce **Přidat certifikát** zaškrtněte políčko pro certifikát, který chcete přidat do Azure front-dveří Standard/Premium. Ponechte výběr verze jako "nejnovější" a vyberte **Přidat**. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="Snímek obrazovky se stránkou přidat certifikát":::

1. Jakmile se certifikát úspěšně zřídí, můžete ho použít při přidávání nové vlastní domény.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="Snímek obrazovky s certifikátem byl úspěšně přidán do tajných klíčů.":::

1. V části *Nastavení* přejděte na **domény** a vyberte **+ Přidat** a přidejte novou vlastní doménu. Na stránce **Přidat doménu** vyberte "Přineste si vlastní certifikát (BYOC)" pro *protokol HTTPS*. V části *tajný klíč* vyberte certifikát, který chcete použít v rozevíracím seznamu. 

    > [!NOTE]
    > Vybraný certifikát musí mít stejný název (CN) stejný jako vlastní doména, kterou chcete přidat.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="Snímek obrazovky s přidáním stránky vlastní domény s protokolem HTTPS":::

1. Postupujte podle pokynů na obrazovce a ověřte certifikát. Pak přiřaďte nově vytvořenou doménu ke koncovému bodu, jak je uvedeno v části [Vytvoření vlastní Příručky k doméně](how-to-add-custom-domain.md) .

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Změna z Azure spravovaného na uvedení vlastního certifikátu (BYOC)

1. Existující spravovaný certifikát Azure můžete změnit na uživatelský certifikát spravovaný tím, že vyberete stav certifikátu a otevřete stránku s **podrobnostmi o certifikátu** .

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="Snímek obrazovky se stavem certifikátu na cílové stránce domény" lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. Na stránce s **podrobnostmi o certifikátu** můžete změnit z možnosti "Azure Managed" na "Přineste si vlastní certifikát (BYOC)". Pak použijte stejný postup jako dříve a zvolte certifikát. Pokud chcete změnit přidružený certifikát k doméně, vyberte **aktualizovat** .

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="Snímek obrazovky se stránkou s podrobnostmi o certifikátu":::

## <a name="next-steps"></a>Další kroky

Přečtěte si o [ukládání do mezipaměti s využitím služby Azure front dveří Standard/Premium](concept-caching.md).
