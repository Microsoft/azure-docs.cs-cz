---
title: Přehled služby Azure spravované aplikace | Microsoft Docs
description: Popisuje koncepce pro Azure spravované aplikace
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/13/2018
ms.author: tomfitz
ms.openlocfilehash: 35e30700270fb5757a80c4a6b8f231042f7640f3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="azure-managed-applications-overview"></a>Přehled spravovaných aplikací Azure

Spravované aplikace Azure umožňují nabízet cloudové řešení, které jsou pro spotřebitele snadno nasadit a provozovat. Implementovat infrastrukturu a poskytovat podporu probíhající. Spravované aplikace zpřístupnit všem zákazníkům, publikování v Azure marketplace. Chcete-li k dispozici jenom na uživatele ve vaší organizaci, publikujte na interní katalogu. 

Spravované aplikace je podobná šablonu řešení na webu Marketplace, s jedním klíče rozdílem. Ve spravované aplikaci jsou zřízené prostředky do skupiny prostředků, který je spravovaný nástrojem vydavatele aplikace. Skupina prostředků je v rámci předplatného v klientu, ale identity v klientovi vydavatele má přístup ke skupině prostředků. Jako vydavatel zadejte náklady na probíhající podpora řešení.

## <a name="advantages-of-managed-applications"></a>Výhody spravovaných aplikací

Spravované aplikace snižte překážek na příjemcům na základě vašich řešení. Nepotřebují odborných znalostí v použít řešení cloudové infrastruktury. Příjemci knihovny mít omezený přístup k důležitým prostředkům. Není nutné se obávat, že uděláte chybu při správě ho. 

Spravované aplikace umožňují vytvořit probíhající vztah s uživatele. Můžete definovat podmínky pro správu aplikace a všechny poplatky jsou zpracovávány prostřednictvím Azure fakturace.

I když zákazníci nasadit tyto spravované aplikace ve svých předplatných, nemají zachovat, aktualizace nebo služba je. Můžete zajistit, že všichni zákazníci používají schválené verze. Zákazníci nemají k vývoji znalostní báze specifické pro aplikaci domény ke správě těchto aplikací. Zákazníci automaticky získat aktualizace aplikace bez nutnosti starat o řešení potíží a diagnostice problémů s aplikacemi. 

Spravované aplikace pro IT týmy, umožní nabízet předem schválených řešení uživatelů v organizaci. Zajistíte, že tato řešení jsou kompatibilní s organizační standardy.

## <a name="types-of-managed-applications"></a>Typy spravovaných aplikací

Ve spravované aplikaci můžete publikovat externě nebo interně.

![Publikovat interně nebo externě](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>katalog služeb

Katalog služeb je katalog interní schválené řešení pro uživatele v organizaci. Katalog používat k zajištění dodržování určitých organizační standardy při jejich poskytování řešení pro organizace. Zaměstnanci používají katalogu snadno zjistit bohatou sadu aplikací, které jsou schváleny podle jejich IT oddělení a doporučené. Zobrazí se jim spravovaných aplikací, které jiní lidé ve své organizaci sdílet s nimi.

Informace o publikování aplikace spravované katalogu služeb najdete v tématu [vytvořit aplikaci služby katalogu](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Dodavatelé chtějí účtovat pošle pro své služby můžete zpřístupnit spravované aplikace prostřednictvím Azure marketplace. Po dodavatele publikuje aplikace, je k dispozici pro uživatele mimo organizaci. K tomuto přístupu, zprostředkovatelé spravované služby (MSPs), nezávislí dodavatelé softwaru (ISV) a systém integrátorech (si) nabízejí svá řešení pro všechny zákazníky využívající Azure.

Informace o publikování spravované aplikace na web Marketplace najdete v tématu [vytvoření aplikace marketplace](publish-marketplace-app.md).

## <a name="resource-groups-for-managed-applications"></a>Skupiny prostředků pro spravované aplikace

Prostředky pro spravované aplikace jsou obvykle umístěny ve dvou skupinách prostředků. Příjemce spravuje jednu skupinu prostředků, a vydavatele jiné skupině prostředků. Při definování spravované aplikace, vydavatele Určuje úrovně přístupu. Následující obrázek ukazuje scénář, kde vydavatele požadavky roli vlastníka pro skupinu spravovaných prostředků. Vydavatele umístit zámek jen pro čtení do této skupiny prostředků pro příjemce. Vydavatel identity, které mají udělen přístup ke skupině spravovaných prostředků se nevztahují zámek.

![Přístup k prostředkům skupiny](./media/overview/access.png)

### <a name="application-resource-group"></a>Skupina prostředků aplikace

Tato skupina prostředků obsahuje instance spravované aplikace. Tato skupina prostředků může obsahovat pouze jeden prostředek. Typ prostředku spravované aplikace je **Microsoft.Solutions/applications**.

Příjemce má plný přístup ke skupině prostředků a používá ke správě životního cyklu spravované aplikace.

### <a name="managed-resource-group"></a>Spravovaná skupina prostředků

Tato skupina prostředků obsahuje všechny prostředky, které jsou vyžadované spravované aplikace. Například tato skupina prostředků obsahuje virtuální počítače, účty úložiště a virtuální sítě pro řešení. Příjemce má omezený přístup do této skupiny prostředků, protože spotřebitel nespravuje jednotlivé prostředky pro spravované aplikace. Vydavatele přístup do této skupiny prostředků odpovídá roli zadaný v definici spravované aplikace. Například vydavatele si mohou vyžádat roli vlastníka nebo přispěvatele pro tuto skupinu prostředků.

Když příjemce odstraní spravované aplikace, je taky odstranit skupinu spravovaných prostředků.

## <a name="next-steps"></a>Další postup

* Úvod k definování a nasazování spravovaných aplikací, najdete v části [vytvořit a nasadit Azure spravované aplikace pomocí rozhraní příkazového řádku Azure](managed-apps-quickstart-cli.md)
* Informace o publikování interní aplikace najdete v tématu [vytvořit aplikaci služby katalogu](publish-service-catalog-app.md).
* Informace o publikování spravovaných aplikací na webu marketplace najdete v tématu [vytvoření aplikace marketplace](publish-marketplace-app.md).
