---
title: Konfigurace brány firewall protokolu IP pro službu Azure Kognitivní hledání
titleSuffix: Azure Cognitive Search
description: Nakonfigurujte zásady řízení IP adres, abyste omezili přístup ke službě Azure Kognitivní hledání.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 98a516e76d5e4f5c225873b934949759d7fcba8d
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100535327"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Konfigurace brány firewall protokolu IP pro Azure Kognitivní hledání

Azure Kognitivní hledání podporuje pravidla protokolu IP pro podporu příchozích bran firewall. Tento model poskytuje další úroveň zabezpečení pro vaši vyhledávací službu podobně jako pravidla protokolu IP, která najdete ve skupině zabezpečení virtuální sítě Azure. Pomocí těchto pravidel IP adres můžete nakonfigurovat vyhledávací službu tak, aby byla dostupná jenom ze schválené sady počítačů a/nebo cloudových služeb. Přístup k datům uloženým ve službě vyhledávání z těchto schválených sad počítačů a služeb bude nadále vyžadovat, aby volající předložil platný autorizační token.

Můžete nastavit pravidla protokolu IP v Azure Portal, jak je popsáno v tomto článku. Alternativně můžete použít [REST API správy verze 2020-03-13](/rest/api/searchmanagement/), [Azure POWERSHELL](/powershell/module/az.search)nebo [Azure CLI](/cli/azure/search).

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Konfigurace brány firewall protokolu IP pomocí Azure Portal

Pokud chcete nastavit zásadu řízení přístupu k IP adresám v Azure Portal, přejděte na stránku služby Azure Kognitivní hledání a v navigační nabídce vyberte **síť** . Připojení k síti koncového bodu musí být **veřejné**. Pokud je vaše připojení nastaveno na **Private**, můžete ke službě vyhledávání přistupovat jenom přes privátní koncový bod.

![Snímek obrazovky, který ukazuje, jak nakonfigurovat bránu firewall protokolu IP v Azure Portal](./media/service-configure-firewall/azure-portal-firewall.png)

Azure Portal poskytuje možnost zadat IP adresy a rozsahy IP adres ve formátu CIDR. Příkladem zápisu CIDR je 8.8.8.0/24, který představuje IP adresy, které jsou v rozsahu od 8.8.8.0 do 8.8.8.255.

> [!NOTE]
> Po povolení zásad řízení přístupu IP pro službu Azure Kognitivní hledání se odmítnou všechny požadavky na rovinu dat z počítačů mimo povolený Seznam rozsahů IP adres. Při konfiguraci pravidel protokolu IP jsou některé funkce Azure Portal zakázané. Budete moct zobrazit a spravovat informace o úrovni služby, ale přístup k indexovým datům a různým součástem ve službě, jako je index, indexer a definice dovednosti, je z bezpečnostních důvodů omezený. Jako alternativu k portálu můžete použít [rozšíření vs Code](https://aka.ms/vscode-search) k interakci s různými komponentami ve službě.

### <a name="requests-from-your-current-ip"></a>Požadavky z vaší aktuální IP adresy

Z důvodu zjednodušení vývoje vám Azure Portal pomůže identifikovat a přidat IP adresu klientského počítače do seznamu povolených aplikací. Aplikace, které běží na vašem počítači, můžou mít přístup ke službě Azure Kognitivní hledání.

Portál automaticky rozpozná vaši IP adresu klienta. Může to být IP adresa klienta počítače nebo brány sítě. Nezapomeňte tuto IP adresu odebrat, než převezmete své zatížení do produkčního prostředí.

Pokud chcete přidat aktuální IP adresu do seznamu IP adres, zkuste **Přidat IP adresu klienta**. Pak vyberte **Uložit**.

![Snímek obrazovky, který ukazuje, jak nakonfigurovat nastavení brány firewall protokolu IP, aby se povolila aktuální IP adresa](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Řešení potíží se zásadami řízení přístupu k IP adresám

Problémy se zásadami řízení přístupu k IP adresám můžete řešit pomocí následujících možností:

### <a name="azure-portal"></a>portál Azure

Povolení zásad řízení přístupu IP pro službu Azure Kognitivní hledání zablokuje všechny požadavky z počítačů mimo povolený Seznam rozsahů IP adres, včetně Azure Portal.  Budete moct zobrazit a spravovat informace o úrovni služby, ale přístup k indexovým datům a různým součástem ve službě, jako je index, indexer a definice dovednosti, je z bezpečnostních důvodů omezený. 

### <a name="sdks"></a>Sady SDK

Při přístupu ke službě Azure Kognitivní hledání pomocí sady SDK z počítačů, které nejsou v seznamu povolených, je vrácena obecná odpověď **403** , která neobsahuje žádné další podrobnosti. Ověřte seznam povolených IP adres pro váš účet a ujistěte se, že je aktualizovaná správná konfigurace pro vaši vyhledávací službu.

## <a name="next-steps"></a>Další kroky

Další informace o přístupu k vyhledávací službě prostřednictvím privátního odkazu najdete v následujícím článku:

* [Vytvoření privátního koncového bodu pro zabezpečené připojení k Azure Kognitivní hledání](service-create-private-endpoint.md)