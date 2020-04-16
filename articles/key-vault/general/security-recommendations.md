---
title: Doporučení zabezpečení pro azure key vault
description: Doporučení zabezpečení pro Azure Key Vault. Provádění těchto pokynů vám pomůže splnit vaše bezpečnostní povinnosti, jak je popsáno v našem modelu sdílené odpovědnosti
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: b7bd85508851df7c7a664862b02141bf4d3bea1f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429847"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Doporučení zabezpečení pro azure key vault

Tento článek obsahuje doporučení zabezpečení pro Azure Key Vault. Implementace těchto doporučení vám pomůže splnit vaše bezpečnostní povinnosti, jak je popsáno v našem modelu sdílené odpovědnosti. Další informace o tom, co společnost Microsoft dělá pro plnění povinností poskytovatele služeb, našlápne [na sdílené odpovědnosti za cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Některá doporučení zahrnutá v tomto článku můžete automaticky sledovat pomocí Azure Security Center. Azure Security Center je první obranná linie ochrany vašich prostředků v Azure. Pravidelně analyzuje stav zabezpečení prostředků Azure k identifikaci potenciálních chyb zabezpečení. Poté poskytuje doporučení, jak je řešit.

- Další informace o doporučeních Azure Security Center najdete [v tématu Doporučení zabezpečení v Azure Security Center](../../security-center/security-center-recommendations.md).
- Informace o Azure Security Center najdete v tématu [Co je Azure Security Center?](../../security-center/security-center-intro.md)

## <a name="data-protection"></a>Ochrana dat

| Doporučení | Komentáře | Security Center |
|-|----|--|
|Povolit obnovitelné odstranění | [Obnovitelné odstranění](overview-soft-delete.md)) umožňuje obnovit odstraněné úschovny a objekty úschovny |  - |
| Omezení přístupu k datům úschovny  | Postupujte podle zásady nejnižších oprávnění a omezení toho, kteří členové vaší organizace mají přístup k datům trezoru |  - |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Omezení počtu uživatelů s přístupem přispěvatele | Pokud má uživatel oprávnění přispěvatele k rovině správy trezoru klíčů, může si udělit přístup k rovině dat nastavením zásad přístupu trezoru klíčů. Měli byste přísně řídit, kdo má přístup k roli přispěvatele k vašim trezorům klíčů. Zajistěte, aby k vašim trezorům měli přístup a spravovali je pouze ti, kteří potřebují přístup k oprávněným osobám. Můžete si [přečíst Zabezpečený přístup k trezoru klíčů](secure-your-key-vault.md)) | - |

## <a name="monitoring"></a>Monitorování

| Doporučení | Komentáře | Security Center |
|-|----|--|
 Měly by být povolené diagnostické protokoly v trezoru klíčů | Povolte protokoly a uchovávejte je až jeden rok. To umožňuje znovu vytvořit stopy aktivit pro účely vyšetřování, když dojde k incidentu zabezpečení nebo dojde k ohrožení sítě. | [Ano](../../security-center/security-center-identity-access.md) |
| Omezení přístupu k protokolům trezoru azure key vault | [Protokoly trezoru klíčů](logging.md)) uložení informací o činnostech prováděných v trezoru, jako je vytvoření nebo odstranění trezorů, klíčů, tajných kódů a může být použito během vyšetřování |  - |

## <a name="networking"></a>Sítě

| Doporučení | Komentáře | Security Center |
|-|----|--|
|Omezit expozici sítě | Přístup k síti by měl být omezen na virtuální sítě používané řešeními, která vyžadují přístup k trezoru. Kontrola informací o [koncových bodech služby Virtuální síť pro azure key vault](overview-vnet-service-endpoints.md)) | - |

## <a name="next-steps"></a>Další kroky

Obraťte se na svého poskytovatele aplikace a zjistěte, zda existují další požadavky na zabezpečení. Další informace o vývoji zabezpečených aplikací naleznete v [tématu Bezpečná vývojová dokumentace](../../security/fundamentals/abstract-develop-secure-apps.md).
