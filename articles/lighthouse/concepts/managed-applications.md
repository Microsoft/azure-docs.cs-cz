---
title: Azure Lighthouse a spravované aplikace Azure
description: Pochopte, jak se dají používat Azure Lighthouse a spravované aplikace Azure společně.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 5c30c3234a57e25ceaa521ad485f58d4d663ebe9
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693971"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse a spravované aplikace Azure

Spravované aplikace Azure i služba Azure Lighthouse fungují tak, že poskytovateli služeb umožní přístup k prostředkům, které se nacházejí v tenantovi zákazníka. Může být užitečné porozumět rozdílům v tom, jak fungují, a scénářům, které pomůžou povolit, a jak se dají použít společně.

> [!TIP]
> I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, můžou podniky, které [spravují víc tenantů](enterprise.md) , používat stejné procesy a nástroje.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Porovnání Azure Lighthouse a spravovaných aplikací Azure

Tato tabulka znázorňuje některé rozdíly vysoké úrovně, které mohou mít vliv na to, jestli se můžete rozhodnout používat Azure Lighthouse nebo Azure Managed Applications. Jak je uvedeno níže, můžete také navrhnout řešení, které je používá společně.

|Aspekty  |Azure Lighthouse  |Spravované aplikace Azure  |
|---------|---------|---------|
|Typický uživatel     |Poskytovatelé služeb nebo podniky spravující víc tenantů         |Nezávislí výrobci softwaru (ISV)         |
|Rozsah přístupu mezi klienty     |Počet předplatných nebo skupin prostředků:         |Skupina prostředků (vymezená pro jednu aplikaci)         |
|K nákupu v Azure Marketplace     |Ne (nabídky se dají publikovat do Azure Marketplace, ale zákazníci se účtují samostatně)        |Ano         |
|Ochrana protokolu IP     |Ano (IP může zůstat v tenantovi poskytovatele služeb)        |Ano (podle návrhu, skupina prostředků je zamčená zákazníkům)         |
|Přiřazení zamítnutí     |Ne         |Ano        |

### <a name="azure-lighthouse"></a>Azure Lighthouse

S [Azure Lighthouse](../overview.md)může poskytovatel služeb provádět nejrůznější úlohy správy přímo na předplatném zákazníka (nebo skupině prostředků). Tento přístup se dosahuje prostřednictvím logické projekce, která umožňuje poskytovatelům služeb přihlašovat se ke svému vlastnímu tenantovi a přistupovat k prostředkům, které patří do tenanta zákazníka. Zákazník může určit, která předplatná nebo skupiny prostředků delegovat poskytovateli služeb, a zákazník zachovává úplný přístup k těmto prostředkům. Můžou taky odebrat přístup poskytovatele služeb kdykoli.

Aby bylo možné používat Azure Lighthouse, jsou zákazníci přihlášeni pro [správu delegovaných prostředků Azure](azure-delegated-resource-management.md) , a to [nasazením šablon ARM](../how-to/onboard-customer.md) nebo pomocí [nabídky spravované služby v Azure Marketplace](managed-services-offers.md). Svůj dopad na zapojení zákazníků můžete sledovat [propojením ID partnera](../how-to/partner-earned-credit.md).

Služba Azure Lighthouse se obvykle používá v případě, kdy poskytovatel služeb provede průběžné úlohy správy pro zákazníky.

### <a name="azure-managed-applications"></a>Spravované aplikace Azure

[Spravované aplikace Azure](../../azure-resource-manager/managed-applications/overview.md) umožňují poskytovateli služeb nebo ISV nabízet cloudová řešení, která zákazníkům můžou snadno nasadit a používat ve svých vlastních předplatných.

Ve spravované aplikaci se prostředky používané aplikací společně seskupují a nasazují do skupiny prostředků spravované vydavatelem. Tato skupina prostředků se nachází v předplatném zákazníka, ale k ní má přístup identita v tenantovi vydavatele. ISV nadále spravuje a udržuje spravovanou aplikaci, zatímco zákazník nemá přímý přístup k práci ve své skupině prostředků nebo jakýkoli přístup k jeho prostředkům.

Spravované aplikace podporují [přizpůsobené prostředí Azure Portal](../../azure-resource-manager/managed-applications/concepts-view-definition.md) a [integraci s vlastními poskytovateli](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md). Tyto možnosti se dají využít k zajištění přizpůsobeného a integrovaného prostředí, které zákazníkům usnadňuje provádění některých úloh správy.

Spravované aplikace je možné [publikovat na Azure Marketplace](../../marketplace/create-new-azure-apps-offer.md), a to buď jako soukromou nabídku pro konkrétního zákazníka, nebo jako veřejné nabídky, které si můžou koupit víc zákazníků. Můžou je taky doručovat uživatelům v rámci vaší organizace [publikováním spravovaných aplikací do katalogu služeb](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md). Katalog služeb i instance Marketplace můžete nasadit pomocí šablon ARM, které můžou zahrnovat jedinečný identifikátor partnera pro komerční tržiště, aby bylo možné sledovat jeho [přidělení](../../marketplace/azure-partner-customer-usage-attribution.md).

Spravované aplikace Azure se obvykle používají pro konkrétního zákazníka, který je možné dosáhnout prostřednictvím řešení klíč, které je plně spravované poskytovatelem služeb.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Používání Azure Lighthouse a spravovaných aplikací Azure společně

I když Azure Lighthouse a Azure Managed Applications používají k dosažení různých cílů různé mechanismy přístupu, můžou nastat scénáře, kdy poskytovatel služeb má k použití obou stejných zákazníků.

Zákazník může třeba chtít spravované služby dodávané poskytovatelem služeb prostřednictvím Azure Lighthouse, aby měli přehled o akcích partnera spolu s pokračující kontrolou jejich delegovaného předplatného. Poskytovatel služeb ale nemusí chtít, aby zákazník měl přístup k určitým prostředkům, které budou uložené v tenantovi zákazníka, nebo na těchto prostředcích přizpůsobit jakékoli přizpůsobené akce. Aby bylo možné splnit tyto cíle, může poskytovatel služeb publikovat soukromou nabídku jako spravovanou aplikaci. Spravovaná aplikace může zahrnovat skupinu prostředků, která je nasazená v tenantovi zákazníka, ale k ní nemůže získat přímý odkaz zákazník.

Zákazníci můžou taky zajímat spravované aplikace od více poskytovatelů služeb, bez ohledu na to, jestli taky používají spravované služby prostřednictvím Azure Lighthouse od kteréhokoli z těchto poskytovatelů služeb. Kromě toho mohou partneři v programu Cloud Solution Provider (CSP) prodávat některé spravované aplikace, které jsou publikovány jiným nezávislým výrobcům softwaru (ISV) zákazníkům, kteří podporují prostřednictvím služby Azure Lighthouse. Díky široké škále možností můžou poskytovatelé služeb zvolit správné saldo pro splnění potřeb svých zákazníků a zároveň omezit přístup k prostředkům, pokud je to vhodné.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [spravovaných aplikacích Azure](../../azure-resource-manager/managed-applications/overview.md).
- Naučte se, jak připojit [předplatné do Azure Lighthouse](../how-to/onboard-customer.md).
- Přečtěte si o [scénářích ISV pomocí Azure Lighthouse](isv-scenarios.md).