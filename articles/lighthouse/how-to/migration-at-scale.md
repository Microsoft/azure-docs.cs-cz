---
title: Správa projektů migrace ve velkém měřítku pomocí Azure Migrate
description: Naučte se efektivně používat Azure Migrate na delegovaných zákaznických zdrojích.
ms.date: 12/4/2020
ms.topic: how-to
ms.openlocfilehash: 16b92f3aa4dc3bfcb71eb232170c4df30348f8db
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095385"
---
# <a name="manage-migration-projects-at-scale-with-azure-migrate"></a>Správa projektů migrace ve velkém měřítku pomocí Azure Migrate

Jako poskytovatel služeb můžete mít k [Azure Lighthouse](../overview.md)k dispozici několik klientů pro zákazníky. Azure Lighthouse umožňuje poskytovatelům služeb provádět operace ve velkém měřítku napříč několika klienty Azure Active Directory (Azure AD) najednou a díky tomu se úlohy správy zefektivňují efektivněji.

[Azure Migrate](../../migrate/migrate-services-overview.md) poskytuje centralizované centrum pro vyhodnocení a migraci na místní servery, infrastrukturu, aplikace a data Azure. Partneři, kteří provádějí posouzení a migraci ve velkém měřítku pro více zákazníků, musí mít k jednotlivým zákaznickým předplatným přístup jednotlivě pomocí [modelu předplatného (Cloud Solution Provider)](/partner-center/customers-revoke-admin-privileges) nebo [vytvořením uživatele typu Host v tenantovi zákazníka](/azure/active-directory/external-identities/what-is-b2b).

Integrace se službou Azure Lighthouse s Azure Migrate umožňuje poskytovatelům služeb zjišťovat, hodnotit a migrovat úlohy pro různé zákazníky ve velkém měřítku a umožnit tak zákazníkům úplnou viditelnost a kontrolu nad jejich prostředími. Prostřednictvím delegované správy prostředků Azure mají poskytovatelé služeb jedno zobrazení všech Azure Migratech projektů, které spravují v rámci více zákaznických klientů.

> [!NOTE]
> Prostřednictvím Azure Lighthouse můžou partneři provádět zjišťování, posuzování a migraci místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V, fyzických serverů a instancí AWS/GCP. Existují dvě možnosti [migrace virtuálních počítačů VMware](../../migrate/server-migrate-overview.md). V současné době je možné při práci na projektu migrace v rámci delegovaného zákazníka použít jenom metodu migrace založenou na agentovi. migrace pomocí replikace bez agentů se v současné době nepodporuje prostřednictvím delegovaného přístupu k oboru zákazníka.

V tomto tématu najdete přehled způsobu použití [Azure Migrate](../../migrate/migrate-services-overview.md) škálovatelným způsobem.

> [!TIP]
> I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, tyto doprovodné materiály se vztahují také na [podniky, které používají Azure Lighthouse ke správě více tenantů](../concepts/enterprise.md).

V závislosti na vašem scénáři můžete chtít vytvořit Azure Migrate v tenantovi zákazníka nebo ve vašem tenantovi pro správu. Projděte si níže uvedené otázky a určete, který model nejlépe vyhovuje potřebám migrace vašeho zákazníka.

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>Vytvoření projektu Azure Migrate v tenantovi zákazníka

Jednou z možností, jak používat Azure Lighthouse, je vytvoření projektu Azure Migrate v tenantovi zákazníka. Uživatelé v tenantovi pro správu pak můžou při vytváření projektu migrace vybrat předplatné zákazníka. Od tenanta pro správu může poskytovatel služeb provádět nezbytné operace migrace. To může zahrnovat nasazení Azure Migrate zařízení pro zjišťování úloh, vyhodnocování úloh seskupení virtuálních počítačů a výpočetních nákladů souvisejících s cloudem, kontrolu připravenosti virtuálních počítačů a provádění migrace.

V tomto scénáři se nevytvoří žádné prostředky a nebudou se ukládat do správy tenanta, a to ani v případě, že je možné iniciovat a provádět kroky zjišťování a vyhodnocení z tohoto tenanta. V rámci předplatného zákazníka budou nasazeny všechny prostředky, jako jsou projekty migrace, sestavy posouzení pro Prem úlohy a migrované prostředky v cílovém cíli. Poskytovatel služeb však může získat přístup ke všem zákaznickým projektům z vlastního tenanta a portálu.

Tento přístup minimalizuje přepínání kontextu pro poskytovatele služeb pracujících v různých zákaznících a zároveň umožňuje zákazníkům zachovat všechny své prostředky ve svých klientech.

Pracovní postup pro tento model bude vypadat přibližně takto:

1. Zákazník je připojen [do Azure Lighthouse](onboard-customer.md). Pro identitu, která se bude používat s Azure Migrate, se vyžaduje předdefinovaná role přispěvatele. Příklad použití této role najdete v ukázkové šabloně [delegované-Resource-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) .
1. Označený uživatel se přihlásí ke správě tenanta v Azure Portal a pak přejde na Azure Migrate. Tento uživatel [vytvoří Azure Migrate projekt](/azure/migrate/create-manage-projects)a vybere odpovídající delegovaný odběr zákazníka.
1. Uživatel pak [provede kroky pro zjišťování a posouzení](../../migrate/tutorial-discover-vmware.md).

   U virtuálních počítačů VMware můžete před konfigurací zařízení omezit zjišťování na vCenter Server datacentra, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé virtuální počítače. Pokud chcete nastavit obor, přiřaďte oprávnění k účtu, který zařízení používá pro přístup k vCenter Server. To je užitečné v případě, že je na hypervisoru hostováno několik virtuálních počítačů zákazníků. Rozsah vyhledávání technologie Hyper-V se nedá omezit.

    > [!NOTE]
    > Můžete zjišťovat a hodnotit virtuální počítače VMware pro migraci pomocí Azure Migrate prostřednictvím delegovaného přístupu, který poskytuje Azure Lighthouse. Pro migraci virtuálních počítačů VMware se v současné době při práci na projektu migrace v rámci delegovaného předplatného zákazníka podporuje jenom metoda založená na agentech.

1. Až bude předplatné cílového zákazníka připravené, pokračujte v migraci prostřednictvím přístupu uděleného službou Azure Lighthouse. Projekt migrace obsahující výsledky posouzení a migrované prostředky se vytvoří v tenantovi zákazníka v rámci cílového předplatného.

> [!TIP]
> Před migrací bude potřeba nasazovat zónu, aby se zřídily základní prostředky infrastruktury, a připravit předplatné, ke kterému se budou migrovat virtuální počítače. Aby bylo možné získat přístup k prostředkům v této cílové zóně nebo je vytvořit, může být vyžadována předdefinovaná role, která se v Azure Lighthouse aktuálně nepodporuje. V takových scénářích může zákazník potřebovat poskytnout roli přístupu hosta nebo delegovat přístup správce prostřednictvím modelu předplatného CSP. Přístup k vytváření víceklientské zóny víceklientské architektury najdete v [ukázkovém řešení pro více tenantů](https://github.com/Azure/Multi-tenant-Landing-Zones) v GitHubu.

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>Vytvoření projektu Azure Migrate ve správě tenanta

V tomto scénáři jsou operace související s migrací, jako je zjišťování a posouzení, stále prováděny uživateli ve správě tenanta. Projekt migrace a všechny relevantní prostředky se ale budou nacházet v partnerském tenantovi a zákazník nebude mít přímý přehled o projektu (i když je to potřeba, můžou hodnocení sdílet se zákazníky). Cílem migrace pro každého zákazníka bude předplatné zákazníka.

Tento přístup umožňuje poskytovatelům služeb rychle spustit zjišťování a projekty vyhodnocení migrace a abstrakcí tyto úvodní kroky od zákaznických předplatných a klientů.

Pracovní postup pro tento model bude vypadat přibližně takto:

1. Zákazník je připojen [do Azure Lighthouse](onboard-customer.md). Pro identitu, která se bude používat s Azure Migrate, se vyžaduje předdefinovaná role přispěvatele. Příklad použití této role najdete v ukázkové šabloně [delegované-Resource-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) .
1. Označený uživatel se přihlásí ke správě tenanta v Azure Portal a pak přejde na Azure Migrate. Tento uživatel [vytvoří Azure Migrate projekt](/azure/migrate/create-manage-projects) v rámci předplatného patřícího ke správě tenanta.
1. Uživatel pak [provede kroky pro zjišťování a posouzení](../../migrate/tutorial-discover-vmware.md). Místní virtuální počítače budou zjišťovány a posouzeny v rámci projektu migrace vytvořeného ve správě tenanta a pak z něj budou migrovány.

   Pokud spravujete více zákazníků na stejném hostiteli Hyper-V, můžete zjistit všechny úlohy najednou. Virtuální počítače specifické pro zákazníka mohou být vybrány ve stejné skupině, pak může být vytvořen odhad a lze provést migraci výběrem příslušného předplatného zákazníka jako cílového umístění. Není nutné omezit rozsah vyhledávání a můžete udržovat úplný přehled všech zákaznických úloh v jednom projektu migrace.

1. Až budete připraveni, pokračujte v migraci tak, že vyberete předplatné delegovaného zákazníka jako cílové cílové umístění pro replikaci a migraci úloh. Nově vytvořené prostředky budou existovat v rámci předplatného zákazníka, zatímco data posouzení a prostředky týkající se projektu migrace zůstanou ve správě tenanta.

Poznámka: před nasazením musíte upravit soubor parametrů tak, aby odrážel vaše prostředí. https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate

## <a name="partner-recognition-for-customer-migrations"></a>Rozpoznávání partnerů pro migrace zákazníků

Jako člen [Microsoft Partner Network](https://partner.microsoft.com)můžete propojit ID partnera s přihlašovacími údaji použitými ke správě prostředků delegovaných zákazníků. Společnost Microsoft může prostřednictvím partnerského propojení pro správce (PAL) ovlivnit jeho vliv a výnosy z Azure na vaši organizaci na základě úloh, které provádíte pro zákazníky, včetně projektů migrace.

Další informace najdete v tématu o [připojení vašeho ID partnera za účelem sledování vašeho dopadu na delegované prostředky](partner-earned-credit.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [Azure Migrate](../../migrate/migrate-services-overview.md).
- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).

