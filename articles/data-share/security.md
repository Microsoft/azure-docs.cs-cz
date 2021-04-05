---
title: Přehled zabezpečení služby Azure Data Share
description: Přehled zabezpečení služby Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 4e62645dd5a7a8336df4fccf12daebc730a91168
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678429"
---
# <a name="security-overview-for-azure-data-share"></a>Přehled zabezpečení služby Azure Data Share

Tento článek poskytuje přehled zabezpečení služby Azure Data Share.

## <a name="security-overview"></a>Přehled zabezpečení

Azure Data Share využívá základní zabezpečení, které Azure nabízí k ochraně dat v klidovém provozu a při přenosu. Data jsou zašifrovaná v klidovém stavu, kde jsou podporovaná podkladovým úložištěm dat. Data se zašifrují i při přenosu pomocí TLS 1,2. Metadata o sdílené složce jsou také šifrována v klidovém stavu a při přenosu. Azure Data Share neukládá obsah zákaznických dat, která jsou sdílená.

Azure Data Share využívá spravovanou identitu (dříve známou jako MSI) k přístupu k úložištím dat, která se používají ke sdílení dat. Neexistuje žádná výměna přihlašovacích údajů mezi poskytovatelem dat a příjemcem dat. Další informace o spravované identitě najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Další informace o rolích a oprávněních potřebných ke sdílení dat najdete v tématu [role a požadavky](concepts-roles-permissions.md).

## <a name="access-control"></a>Řízení přístupu

Řízení přístupu ke sdílené složce Azure Data Share lze nastavit na úrovni prostředku sdílení dat, aby bylo zajištěno, že k nim budou mít přístup pouze oprávnění, která jsou autorizována. Vlastník a přispěvatel prostředku pro sdílení dat můžou sdílet data, přijímat sdílené složky a provádět změny ve stávajících sdílených složkách. Čtenář prostředku pro sdílení dat může zobrazit sdílené složky, ale nemůže provádět změny. 

Po vytvoření nebo přijetí sdílené složky můžou změny provádět uživatelé s řádným oprávněním ke sdílení dat. Pokud uživatel, který vytvoří nebo přijme sdílenou složku, odejde z organizace, neukončí sdílení nebo zastaví tok dat. Jiní uživatelé, kteří mají správné oprávnění k prostředku sdílení dat, můžou dál spravovat sdílenou složku.

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Sdílení dat z nebo do úložišť dat s povolenou bránou firewall
Pokud chcete sdílet data z nebo do účtů úložiště s bránou firewall zapnutou, musíte povolit v účtu úložiště **Povolit důvěryhodné služby Microsoftu** . Podrobnosti najdete v tématu [konfigurace Azure Storage bran firewall a virtuálních sítí](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) .


## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .
