---
title: Přehled zabezpečení pro Azure Data Share
description: Přehled zabezpečení pro Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: e299f63777847c557fc13afb4d36d01c12b3b52a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84637120"
---
# <a name="security-overview-for-azure-data-share"></a>Přehled zabezpečení pro Azure Data Share

Tento článek poskytuje přehled zabezpečení služby Azure Data Share.

## <a name="security-overview"></a>Přehled zabezpečení

Azure Data Share využívá základní zabezpečení, které Azure nabízí k ochraně dat v klidovém provozu a při přenosu. Data jsou zašifrovaná v klidovém stavu, kde jsou podporovaná podkladovým úložištěm dat. Data se šifrují i při přenosu. Metadata o sdílené složce jsou také šifrována v klidovém stavu a při přenosu. 

Řízení přístupu lze nastavit na úrovni prostředků sdílené složky Azure, aby bylo zajištěno, že k nim mají přístup autorizované aplikace. 

Azure Data Share využívá spravovanou identitu (dříve známou jako MSI) k přístupu k úložištím dat, která se používají ke sdílení dat. Neexistuje žádná výměna přihlašovacích údajů mezi poskytovatelem dat a příjemcem dat. Další informace o spravované identitě najdete v tématu [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). Další informace o rolích a oprávněních potřebných ke sdílení dat najdete v tématu [role a požadavky](concepts-roles-permissions.md).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .




