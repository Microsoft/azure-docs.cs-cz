---
title: Přehled zabezpečení služby Azure Data Share
description: Přehled zabezpečení služby Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 5d815c27ecc7825f0bc1e6772654b094a799b63d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216551"
---
# <a name="security-overview-for-azure-data-share"></a>Přehled zabezpečení služby Azure Data Share

Tento článek poskytuje přehled zabezpečení služby Azure Data Share.

## <a name="security-overview"></a>Přehled zabezpečení

Azure Data Share využívá základní zabezpečení, které Azure nabízí k ochraně dat v klidovém provozu a při přenosu. Data jsou zašifrovaná v klidovém stavu, kde jsou podporovaná podkladovým úložištěm dat. Data se šifrují i při přenosu. Metadata o sdílené složce jsou také šifrována v klidovém stavu a při přenosu. 

Řízení přístupu lze nastavit na úrovni prostředků sdílené složky Azure, aby bylo zajištěno, že k nim mají přístup autorizované aplikace. 

Azure Data Share využívá spravovanou identitu (dříve známou jako MSI) k přístupu k úložištím dat, která se používají ke sdílení dat. Neexistuje žádná výměna přihlašovacích údajů mezi poskytovatelem dat a příjemcem dat. Další informace o spravované identitě najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Další informace o rolích a oprávněních potřebných ke sdílení dat najdete v tématu [role a požadavky](concepts-roles-permissions.md).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .