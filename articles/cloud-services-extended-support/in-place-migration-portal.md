---
title: Postup migrace – portál
description: Postup migrace na Cloud Services (Rozšířená podpora) pomocí Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 79889b08baa80dc67b30ae445004e37d9f9fe295
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286837"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>Migrace na Cloud Services (Rozšířená podpora) pomocí Azure Portal

V tomto článku se dozvíte, jak použít Azure Portal k migraci z [Cloud Services (Classic)](../cloud-services/cloud-services-choose-me.md) na [Cloud Services (Rozšířená podpora)](overview.md).

> [!IMPORTANT]
> Migrace z Cloud Services (Classic) na Cloud Services (Rozšířená podpora) pomocí nástroje pro migraci je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Než začnete

**Ujistěte se, že jste správcem předplatného.**

K provedení této migrace musíte být přidáni jako spolusprávce předplatného v [Azure Portal](https://portal.azure.com).

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce **centra** vyberte **předplatné**. Pokud ho nevidíte, vyberte **všechny služby**.
3. Vyhledejte odpovídající položku pro odběr a potom se podívejte do pole **Moje role** . Pro spolusprávce by měla být hodnota *správce účtu*.

Pokud nemůžete přidat spolusprávce, obraťte se na správce služby nebo [spolupracujícího správce](../role-based-access-control/classic-administrators.md) , aby vás přidal.

**Zaregistrujte se do poskytovatele prostředků migrace.**

1. Zaregistrujte se pomocí funkce poskytovatel prostředků migrace `Microsoft.ClassicInfrastructureMigrate` a verze Preview `Cloud Services` v oboru názvů Microsoft. compute pomocí [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider-1).  
1. Počkejte pět minut, než se registrace dokončí, a zkontrolujte stav schválení. 

## <a name="migrate-your-cloud-service-resources"></a>Migrace prostředků cloudové služby

1. Přejít na [okno portálu Cloud Services (Classic)](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames). 
2. Vyberte cloudovou službu, kterou chcete migrovat.
3. Vyberte okno **migrovat do ARM** .

    > [!NOTE]
    > Pokud migrujete Cloud Services (Classic) umístěnou ve virtuální síti (Classic), zobrazí se zpráva s výzvou k přesunutí okna virtuální síť (Classic).
    > Do okna virtuální síť (Classic) přejdete, abyste dokončili migraci virtuální sítě (Classic) i nasazení Cloud Services (Classic) v rámci této služby.
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="Obrázek ukazuje přesunutí virtuální sítě v Azure Portal Classic.":::
 

4. Ověřte migraci. 

    Pokud je ověření úspěšné, jsou všechna nasazení podporovaná a připravené k přípravě.  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="Obrázek ukazuje okno migrace do ARM v Azure Portal.":::

    Pokud se ověření nezdaří, zobrazí se seznam nepodporovaných scénářů, které je potřeba před pokračováním migrace opravit. 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="Obrázek ukazuje chybu ověřování v Azure Portal.":::

5. Připravte se na migraci.

    Pokud je příprava úspěšná, migrace je připravena k potvrzení.
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="Image zobrazuje předání ověření v Azure Portal.":::

    Pokud se příprava nepovede, přečtěte si chybu, vyřešte všechny problémy a zkuste operaci připravit znovu. 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="Obrázek ukazuje chybu selhání ověřování.":::

      Po přípravě se k dispozici všechny Cloud Services ve virtuální síti pro operace čtení pomocí okna Azure Portal Cloud Services (Classic) i Cloud Services (rozšířené podpory). Nasazení cloudové služby (rozšířené podpory) se teď dá testovat, aby se zajistilo správné fungování před dokončením migrace. 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="Image zobrazuje rozhraní API pro testování v okně portálu.":::

6.  **(Volitelné)** Přerušte migraci. 
    
    Pokud se rozhodnete ukončit migraci, použijte tlačítko **přerušit** a vraťte předchozí kroky zpátky. Nasazení Cloud Services (Classic) je pak odemčeno pro všechny operace CRUD.

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="Image zobrazuje předávání ověřování.":::

    Pokud se přerušení nepovede, vyberte **Opakovat přerušení**. Opakování by měl problém vyřešit. Pokud ne, obraťte se na podporu. 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="Image zobrazuje chybovou zprávu o selhání ověřování.":::

7.  Potvrďte migraci.

    >[!IMPORTANT]
    > Po potvrzení migrace není k dispozici žádný způsob, jak se vrátit. 
    
    Zadáním možnosti Ano potvrďte a potvrďte migraci. Migrace je teď dokončená. Migrované nasazení Cloud Services (Rozšířená podpora) je odemčené pro všechny operace. 

## <a name="next-steps"></a>Další kroky
V části [po změnách migrace](in-place-migration-overview.md#post-migration-changes) si můžete prohlédnout změny v souborech nasazení, automatizaci a dalších atributech nového nasazení Cloud Services (rozšířené podpory). 
