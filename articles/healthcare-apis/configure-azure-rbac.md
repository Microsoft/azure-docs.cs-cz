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
ms.openlocfilehash: 1df2d112814c93261c364883f4b834e0bdb657bb
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839907"
---
# <a name="configure-azure-rbac-for-fhir"></a>Konfigurace služby Azure RBAC pro FHIR 

V tomto článku se dozvíte, jak pomocí [řízení přístupu založeného na rolích Azure (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/) přiřadit přístup k rozhraní Azure API pro rovinu dat FHIR. Azure RBAC je preferované metody pro přiřazení přístupu k rovině dat, když se uživatelé roviny dat spravují v tenantovi Azure Active Directory přidruženého k vašemu předplatnému Azure. Pokud používáte externího tenanta Azure Active Directory, přečtěte si [odkaz na místní přiřazení RBAC](configure-local-rbac.md).

## <a name="confirm-azure-rbac-mode"></a>Potvrďte režim Azure RBAC

Pokud chcete používat službu Azure RBAC, musí být vaše rozhraní API Azure pro FHIR nakonfigurované tak, aby používalo vašeho tenanta předplatného Azure pro rovinu dat a neměli jim přiřazená ID objektů identity. Nastavení můžete ověřit kontrolou okna **ověřování** rozhraní API Azure pro FHIR:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Potvrďte režim Azure RBAC":::

**Autorita** by měla být nastavená na tenanta Azure Active Directory, který je přidružený k vašemu předplatnému, a v poli by se neměly používat žádné identifikátory GUID s popiskem **povolených ID objektů**. Všimněte si také, že je políčko zakázané a popisek indikuje, že by se měla použít Azure RBAC k přiřazení rolí roviny dat.

## <a name="assign-roles"></a>Přiřazení rolí

Pokud chcete udělit uživatelům, instančním objektům nebo skupinám přístup k rovině dat FHIR, klikněte na **řízení přístupu (IAM)**, pak na **přiřazení rolí** a klikněte na **+ Přidat**:

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Potvrďte režim Azure RBAC":::

V části Výběr **role** vyhledejte jednu z předdefinovaných rolí pro rovinu dat FHIR:

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="Potvrďte režim Azure RBAC":::

Můžete si vybrat mezi:

* Čtecí modul dat FHIR: může číst (a Hledat) FHIR data.
* Zapisovač dat FHIR: může číst, zapisovat a dočasná odstranění FHIR dat.
* FHIR data Exportér: může číst a exportovat ( `$export` operátor) data.
* Přispěvatel dat FHIR: může provádět všechny operace roviny dat.

Pokud tyto role pro vaše potřeby nestačí, můžete také [vytvořit vlastní role](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell).

V poli **Vybrat** vyhledejte uživatele, instanční objekt nebo skupinu, ke kterým chcete přiřadit roli.

## <a name="caching-behavior"></a>Chování při ukládání do mezipaměti

Rozhraní Azure API pro FHIR zapíše do mezipaměti rozhodnutí po dobu až 5 minut. Pokud udělíte uživateli přístup k FHIR serveru tím, že je přidáte do seznamu povolených ID objektů nebo je odeberete ze seznamu, měli byste očekávat, že může trvat až pět minut, než se změny v oprávněních šíří.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak přiřadit role Azure pro rovinu dat FHIR. Další informace o dalších nastaveních pro Azure API pro FHIR:
 
>[!div class="nextstepaction"]
>[Další nastavení pro Azure API pro FHIR](azure-api-for-fhir-additional-settings.md)
