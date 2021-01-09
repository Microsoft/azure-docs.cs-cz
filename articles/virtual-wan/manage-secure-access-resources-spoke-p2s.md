---
title: Správa zabezpečeného přístupu k prostředkům v paprskových virtuální sítě pro klienty P2S
titleSuffix: Azure Virtual WAN
description: Tento článek vám pomůže použít Azure Virtual WAN a pravidla Azure Firewall ke správě zabezpečeného přístupu k virtuálním sítím pro klienty VPN uživatelů (Point-to-site).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 12/11/2020
ms.author: cherylmc
ms.openlocfilehash: c2efd9ac137c226c1d3a77e2cb6ebe17d75cb496
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051526"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>Správa zabezpečeného přístupu k prostředkům v paprskových virtuální sítě pro klienty VPN uživatelů

V tomto článku se dozvíte, jak používat pravidla a filtry pro virtuální síť WAN a Azure Firewall ke správě zabezpečeného přístupu pro připojení k prostředkům v Azure prostřednictvím připojení Point-to-site IKEv2 nebo Open VPN. Tato konfigurace je užitečná, pokud máte vzdálené uživatele, pro které chcete omezit přístup k prostředkům Azure, nebo zabezpečit prostředky v Azure.

Kroky v tomto článku vám pomůžou vytvořit architekturu v následujícím diagramu, abyste klientům VPN umožnili přístup k určitému prostředku (VM1) ve virtuální síti paprsku připojené k virtuálnímu rozbočovači, ale ne k dalším prostředkům (VM2). Použijte tento příklad architektury jako základní pokyn.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="Diagram: zabezpečený virtuální rozbočovač" :::

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* Máte k dispozici hodnoty pro konfiguraci ověřování, kterou chcete použít. Například server RADIUS, Azure Active Directory ověřování nebo [vygenerujte a exportujte certifikáty](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

## <a name="create-a-virtual-wan"></a>Vytvoření virtuální sítě WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>Definovat parametry konfigurace P2S

Konfigurace Point-to-Site (P2S) definuje parametry pro připojení vzdálených klientů. Tato část vám pomůže definovat parametry konfigurace P2S a pak vytvořit konfiguraci, která se bude používat pro profil klienta VPN. Postup závisí na metodě ověřování, kterou chcete použít.

### <a name="authentication-methods"></a>Metody ověřování

Při výběru metody ověřování máte tři možnosti. Každá z metod má konkrétní požadavky. Vyberte jednu z následujících metod a pak dokončete postup.

* **Ověřování Azure Active Directory:** Získejte následující:

   * **ID aplikace** podnikové sítě VPN zaregistrované ve vašem Tenantovi služby Azure AD.
   * **Vystavitel**. Příklad: `https://sts.windows.net/your-Directory-ID`.
   * **Tenant služby Azure AD**. Příklad: `https://login.microsoftonline.com/your-Directory-ID`.

* **Ověřování pomocí protokolu RADIUS:** Získejte IP adresu serveru RADIUS, tajný klíč serveru RADIUS a informace o certifikátu.

* **Certifikáty Azure:** Pro tuto konfiguraci se vyžadují certifikáty. Musíte buď vygenerovat nebo získat certifikáty. Pro každého klienta je vyžadován klientský certifikát. Kromě toho je potřeba nahrát informace o kořenovém certifikátu (veřejný klíč). Další informace o vyžadovaných certifikátech najdete v tématu [generování a export certifikátů](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

Následující příklad ukazuje ověřování certifikátů Azure.

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>Vytvoření centra a brány

V této části vytvoříte virtuální rozbočovač s bránou Point-to-site. Při konfiguraci nástroje můžete použít následující příklad hodnot:

* **Privátní adresní prostor IP adres centra:** 10.0.0.0/24
* **Fond adres klienta:** 10.5.0.0/16
* **Vlastní servery DNS:** Můžete uvést až 5 serverů DNS.

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generování konfiguračních souborů klienta VPN

V této části vygenerujete a stáhnete soubory konfiguračního profilu. Tyto soubory se používají ke konfiguraci nativního klienta VPN v klientském počítači. Informace o obsahu souborů profilů klienta najdete v tématu [Konfigurace Point-to-site-Certificates](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md).

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>Konfigurace klientů VPN

Pomocí staženého profilu nakonfigurujte klienty pro vzdálený přístup. Postup pro každý operační systém je jiný, postupujte podle pokynů, které se vztahují k vašemu systému.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>Připojení virtuální sítě rozbočovače

V této části připojíte virtuální síť rozbočovače k virtuální síti WAN.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>Vytvoření virtuálních počítačů

V této části vytvoříte dva virtuální počítače ve virtuální síti, VM1 a VM2. V diagramu sítě používáme 10.18.0.4 a 10.18.0.5. Při konfiguraci virtuálních počítačů se ujistěte, že jste vybrali virtuální síť, kterou jste vytvořili (najdete na kartě síť). Postup vytvoření virtuálního počítače najdete v tématu [rychlý Start: Vytvoření virtuálního počítače](../virtual-machines/windows/quick-create-portal.md).

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>Zabezpečit virtuální rozbočovač

Standardní virtuální rozbočovač nemá integrované zásady zabezpečení pro ochranu prostředků ve virtuálních sítích s paprsky. Zabezpečený virtuální rozbočovač používá pro správu příchozího a odchozího provozu pro ochranu vašich prostředků v Azure Azure Firewall nebo poskytovatele třetí strany.

Převeďte centrum na zabezpečené centrum pomocí následujícího článku: [konfigurace Azure firewall ve virtuálním centru WAN](howto-firewall.md).

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> Vytvoření pravidel pro správu a filtrování provozu

Vytvořte pravidla, která nadiktují chování Azure Firewall. Díky zabezpečení centra zajišťujeme, aby všechny pakety, které vstupují do virtuálního rozbočovače, byly před přístupem k prostředkům Azure podléhat zpracování branou firewall.

Po dokončení tohoto postupu budete mít vytvořenou architekturu, která uživatelům sítě VPN umožní přístup k virtuálnímu počítači s privátní IP adresou 10.18.0.4, ale **ne** přístup k virtuálnímu počítači pomocí privátní IP adresy 10.18.0.5.

1. V Azure Portal přejděte na **Správce brány firewall**.
1. V části zabezpečení vyberte **Azure firewall zásady**.
1. Vyberte **vytvořit Azure firewall zásady**.
1. V části **Podrobnosti o zásadách** zadejte název a vyberte oblast, ve které je virtuální rozbočovač nasazený.
1. Vyberte **Další: nastavení DNS (Preview)**.
1. Vyberte **Další: pravidla**.
1. Na kartě **pravidla** vyberte **přidat kolekci pravidel**.
1. Zadejte název kolekce. Nastavte typ jako **síť**. Přidejte hodnotu priority **100**.
1. Vyplňte název pravidla, typ zdroje, zdroj, protokol, cílové porty a typ cíle, jak je znázorněno v následujícím příkladu. Pak vyberte **Přidat**. Toto pravidlo umožňuje všem IP adresám z fondu klientů VPN získat přístup k virtuálnímu počítači s 10.18.04 privátních IP adres, ale ne k žádnému jinému prostředku připojenému k virtuálnímu rozbočovači. Vytvořte libovolná požadovaná pravidla, která budou vyhovovat vašim požadovaným architekturám a pravidlům oprávnění.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="Pravidla brány firewall" :::

1. Vyberte **Další: Analýza hrozeb**.
1. Vyberte **Další: rozbočovače**.
1. Na kartě **centra** vyberte **přidružit virtuální rozbočovače**.
1. Vyberte virtuální rozbočovač, který jste vytvořili dříve, a pak vyberte **Přidat**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

Dokončení tohoto procesu může trvat 5 minut nebo déle.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Směrování provozu prostřednictvím Azure Firewall

V této části je potřeba zajistit, aby byl provoz směrován prostřednictvím Azure Firewall.

1. Na portálu v části **Správce brány firewall** vyberte **Zabezpečená virtuální rozbočovače**.
1. Vyberte virtuálního rozbočovače, který jste vytvořili.
1. V části **Nastavení** vyberte **Konfigurace zabezpečení**.
1. V části **privátní přenos** vyberte **Odeslat prostřednictvím Azure firewall**.
1. Ověřte, zda je připojení virtuální sítě a privátní přenos připojení k síti zajištěno pomocí Azure Firewall.
1. Vyberte **Uložit**.

## <a name="validate"></a><a name="validate"></a>Ověření

Ověřte nastavení zabezpečeného centra.

1. Připojte se k **zabezpečenému virtuálnímu centru** přes VPN z klientského zařízení.
1. Odešlete žádost o IP adresu **10.18.0.4** z vašeho klienta. Měla by se zobrazit odpověď.
1. Odešlete žádost o IP adresu **10.18.0.5** z vašeho klienta. Nemusíte být schopni zobrazit odpověď.

### <a name="considerations"></a>Požadavky

* Ujistěte se, že **tabulka efektivních tras** na zabezpečeném virtuálním rozbočovači má další segment směrování privátních přenosů přes bránu firewall. Přístup k tabulce efektivní trasy získáte tak, že přejdete do svého prostředku **virtuálního rozbočovače** . V části **připojení** vyberte **Směrování** a pak vyberte **platné trasy**. Odtud vyberte **výchozí** směrovací tabulku.
* Ověřte, že jste v části [vytvořit pravidla](#create-rules) vytvořili pravidla. Pokud se tyto kroky neobjeví, pravidla, která jste vytvořili, se ve skutečnosti nepřidruží k centru a směrovací tabulka a tok paketů nebudou používat Azure Firewall.

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v [nejčastějších dotazech k virtuální síti WAN](virtual-wan-faq.md).
* Další informace o Azure Firewall najdete v části [Nejčastější dotazy k Azure firewall](../firewall/firewall-faq.yml).
