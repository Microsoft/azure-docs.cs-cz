---
title: 'Rychlý Start: nasazení rozhraní Azure API pro FHIR pomocí Azure Portal'
description: V tomto rychlém startu se dozvíte, jak nasadit rozhraní API Azure pro FHIR a nakonfigurovat nastavení pomocí Azure Portal.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 03/15/2020
ms.author: cavoeg
ms.openlocfilehash: cabacd5204f4a9ac5c17c7bd66924482b5bf688a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023461"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Rychlý Start: nasazení rozhraní Azure API pro FHIR pomocí Azure Portal

V tomto rychlém startu se dozvíte, jak nasadit rozhraní API Azure pro FHIR pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="create-new-resource"></a>Vytvořit nový prostředek

Otevřete [Azure Portal](https://portal.azure.com) a klikněte na **vytvořit prostředek** .

![Vytvoření prostředku](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Hledání rozhraní API Azure pro FHIR

Rozhraní Azure API pro FHIR můžete najít zadáním "FHIR" do vyhledávacího pole:

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="Hledání rozhraní API pro zdravotní péči":::

## <a name="create-azure-api-for-fhir-account"></a>Vytvoření Azure API pro účet FHIR

Vyberte **vytvořit** a vytvořte nový Azure API pro účet FHIR:

:::image type="content" source="media/quickstart-paas-portal/portal-create-healthcare-apis.png" alt-text="Vytvoření Azure API pro účet FHIR":::

## <a name="enter-account-details"></a>Zadejte podrobnosti účtu.

Vyberte existující skupinu prostředků nebo vytvořte novou, zvolte název účtu a nakonec klikněte na **zkontrolovat + vytvořit**:

:::image type="content" source="media/quickstart-paas-portal/portal-new-healthcareapi-details.png" alt-text="Nové podrobnosti o rozhraní API pro zdravotnictví":::

Potvrďte vytváření a očekává se nasazení rozhraní API FHIR.

## <a name="additional-settings-optional"></a>Další nastavení (volitelné)

Můžete také kliknout na **Další: Další nastavení** a zobrazit nastavení ověřování. Výchozí konfigurací rozhraní API Azure pro FHIR je [použití služby Azure RBAC pro přiřazení rolí roviny dat](configure-azure-rbac.md). Při konfiguraci v tomto režimu bude "autorita" pro službu FHIR nastavena na tenanta Azure Active Directory předplatného:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode-create.png" alt-text="Výchozí nastavení ověřování":::

Všimněte si, že pole pro zadání povolených ID objektů je šedé, protože používáme Azure RBAC pro konfiguraci přiřazení rolí v tomto případě.

Pokud chcete službu FHIR nakonfigurovat tak, aby používala externího nebo sekundárního tenanta Azure Active Directory, můžete změnit autoritu a zadat ID objektů pro uživatele a skupiny, kteří mají mít povolený přístup k serveru. Další informace najdete v tématu Průvodce [konfigurací místních RBAC](configure-local-rbac.md) .

## <a name="fetch-fhir-api-capability-statement"></a>Načíst příkaz schopnosti rozhraní API FHIR

Pokud chcete ověřit, jestli je nový účet FHIR API zřízený, načtěte příkaz funkce tak, že přejdete na prohlížeč `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, Azure API pro FHIR a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků obsahující Azure API pro účet FHIR, vyberete **Odstranit skupinu prostředků** a pak ověříte název skupiny prostředků, která se má odstranit.

## <a name="next-steps"></a>Další kroky

V této příručce pro rychlý Start jste nasadili rozhraní Azure API pro FHIR do svého předplatného. Pokud chcete nastavit další nastavení v rozhraní API Azure pro FHIR, přejděte k Průvodci dalšími nastaveními. Pokud jste připraveni začít používat rozhraní API Azure pro FHIR, přečtěte si další informace o tom, jak registrovat aplikace.

>[!div class="nextstepaction"]
>[Další nastavení v rozhraní API Azure pro FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Přehled registrace aplikací](fhir-app-registration.md)
