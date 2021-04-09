---
title: Azure Firewall Premium Preview v Azure Portal
description: Přečtěte si o Azure Firewall Premium Preview v Azure Portal.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100549614"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Azure Firewall Premium Preview v Azure Portal

> [!IMPORTANT]
> Azure Firewall Premium je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Firewall Premium Preview je brána firewall nové generace s funkcemi, které jsou potřeba pro vysoce citlivá a regulovaná prostředí. Zahrnuje následující funkce:

- **Kontrola TLS** – dešifruje odchozí přenosy, zpracovává data a pak data zašifruje a pošle je do cíle.
- **Zprostředkovatelů identity** – systém zjišťování a prevence vniknutí k síti (zprostředkovatelů identity) umožňuje sledovat síťové aktivity pro škodlivou aktivitu, protokolovat informace o této aktivitě, nahlásit je a volitelně se pokusit o její blokování.
- **Filtrování adresy URL** – rozšiřuje schopnost filtrování plně kvalifikovaného názvu domény Azure firewall, aby bylo možné zvážit celou adresu URL. Například `www.contoso.com/a/c` místo `www.contoso.com` .
- **Webové kategorie** – správci můžou povolit nebo odepřít přístup uživatelům k kategoriím webu, jako jsou weby hazardních her, weby sociálních médií a další.

Další informace najdete v tématu [Azure firewall Premium – funkce](premium-features.md).

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

Nasazení verze Azure Firewall Premium Preview se podobá nasazení standardní Azure Firewall:

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="nasazení portálu":::

Pro **úroveň brány firewall** vyberte **Premium (Preview)** a pro **zásady brány firewall** vyberte existující zásadu Premium nebo vytvořte novou.

## <a name="configure-the-premium-policy"></a>Konfigurace zásad Premium

Konfigurace zásad brány firewall úrovně Premium je podobná konfiguraci standardních zásad brány firewall. Pomocí zásad Premium můžete nakonfigurovat prémiové funkce:

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Nasazení zásad Premium":::

### <a name="rule-configuration"></a>Konfigurace pravidla

Při konfiguraci pravidel aplikací v zásadě Premium můžete nakonfigurovat přidání prémiových funkcí:

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Pravidlo úrovně Premium":::

## <a name="next-steps"></a>Další kroky

Pokud chcete zobrazit funkce Azure Firewall Premium Preview v akci, přečtěte si téma [nasazení a konfigurace Azure firewall Premium Preview](premium-deploy.md).