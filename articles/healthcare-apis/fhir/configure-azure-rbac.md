---
title: Konfigurace řízení přístupu na základě role v Azure (Azure RBAC) pro Azure API pro FHIR
description: Tento článek popisuje, jak nakonfigurovat Azure RBAC pro rovinu dat Azure API pro FHIR.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.reviewer: dseven
ms.openlocfilehash: fb57b689b77ec4cc6205c8885c2a2e062c469efb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018080"
---
# <a name="configure-azure-rbac-for-fhir"></a>Konfigurace služby Azure RBAC pro FHIR 

V tomto článku se dozvíte, jak pomocí [řízení přístupu založeného na rolích Azure (Azure RBAC)](../../role-based-access-control/index.yml) přiřadit přístup k rozhraní Azure API pro rovinu dat FHIR. Azure RBAC je preferované metody pro přiřazení přístupu k rovině dat, když se uživatelé roviny dat spravují v tenantovi Azure Active Directory přidruženého k vašemu předplatnému Azure. Pokud používáte externího tenanta Azure Active Directory, přečtěte si [odkaz na místní přiřazení RBAC](configure-local-rbac.md).

## <a name="confirm-azure-rbac-mode"></a>Potvrďte režim Azure RBAC

Pokud chcete používat službu Azure RBAC, musí být vaše rozhraní API Azure pro FHIR nakonfigurované tak, aby používalo vašeho tenanta předplatného Azure pro rovinu dat a neměli jim přiřazená ID objektů identity. Nastavení můžete ověřit kontrolou okna **ověřování** rozhraní API Azure pro FHIR:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Potvrďte režim Azure RBAC":::

**Autorita** by měla být nastavená na tenanta Azure Active Directory, který je přidružený k vašemu předplatnému, a v poli by se neměly používat žádné identifikátory GUID s popiskem **povolených ID objektů**. Všimněte si také, že je políčko zakázané a popisek indikuje, že by se měla použít Azure RBAC k přiřazení rolí roviny dat.

## <a name="assign-roles"></a>Přiřazení rolí

Pokud chcete udělit uživatelům, instančním objektům nebo skupinám přístup k rovině dat FHIR, klikněte na **řízení přístupu (IAM)**, pak na **přiřazení rolí** a klikněte na **+ Přidat**:

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Přidat přiřazení role Azure":::

V části Výběr **role** vyhledejte jednu z předdefinovaných rolí pro rovinu dat FHIR:

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="Předdefinované role FHIR dat":::

Můžete si vybrat mezi možnostmi:

* Čtecí modul dat FHIR: může číst (a Hledat) FHIR data.
* Zapisovač dat FHIR: může číst, zapisovat a dočasná odstranění FHIR dat.
* FHIR data Exportér: může číst a exportovat ( `$export` operátor) data.
* Přispěvatel dat FHIR: může provádět všechny operace roviny dat.

Pokud tyto role pro vaše potřeby nestačí, můžete také [vytvořit vlastní role](../../role-based-access-control/tutorial-custom-role-powershell.md).

V poli **Vybrat** vyhledejte uživatele, instanční objekt nebo skupinu, ke kterým chcete přiřadit roli.

## <a name="caching-behavior"></a>Chování při ukládání do mezipaměti

Rozhraní Azure API pro FHIR zapíše do mezipaměti rozhodnutí po dobu až 5 minut. Pokud udělíte uživateli přístup k FHIR serveru tím, že je přidáte do seznamu povolených ID objektů nebo je odeberete ze seznamu, měli byste očekávat, že může trvat až pět minut, než se změny v oprávněních šíří.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak přiřadit role Azure pro rovinu dat FHIR. Další informace o dalších nastaveních pro Azure API pro FHIR:
 
>[!div class="nextstepaction"]
>[Další nastavení pro Azure API pro FHIR](azure-api-for-fhir-additional-settings.md)