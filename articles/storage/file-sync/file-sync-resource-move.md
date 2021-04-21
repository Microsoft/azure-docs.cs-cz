---
title: Synchronizace souborů Azure přesunu prostředků a změny topologie
description: Naučte se, jak přesunout prostředky synchronizace mezi skupinami prostředků, předplatnými a Azure Active Directory (AAD).
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb59b8d48d8411ade620304fbd143d86ac5ae919
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796779"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Přesunutí prostředků Synchronizace souborů Azure do jiné skupiny prostředků, předplatného nebo tenanta AAD

Tento článek popisuje, jak provést změny v tenantovi skupina prostředků, předplatné nebo Azure Active Directory (AAD) pro vaše Synchronizace souborů Azure cloudové prostředky a účty Azure Storage.

Při plánování provádění změn v cloudových prostředcích Synchronizace souborů Azure je důležité zvážit prostředky úložiště ve stejnou dobu. Existují následující prostředky:

**Prostředky Synchronizace souborů Azure (v hierarchickém pořadí)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Služba synchronizace úložiště
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Registrovaný Server
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Skupina synchronizace
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Koncový bod cloudu
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Koncový bod serveru

V Synchronizace souborů Azure je jediným prostředkem, který se dá přesunout, prostředek služby synchronizace úložiště. Všechny podprostředky jsou vázány na své nadřazené položky a nelze je přesunout do jiné služby synchronizace úložiště.

**Prostředky služby Azure Storage (v hierarchickém pořadí)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Účet úložiště
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Sdílení souborů

V Azure Storage jediným prostředkem, který se dá přesunout, je účet úložiště. Sdílenou složku Azure jako subprostředkům nejde přesunout do jiného účtu úložiště.

## <a name="supported-combinations"></a>Podporované kombinace

Při plánování přesunu prostředků je potřeba zvážit, že účet úložiště a prostředek nejvyšší úrovně Synchronizace souborů Azure prostředku, který se nazývá *Služba synchronizace úložiště*, se musí považovat za společně.

Osvědčeným postupem je, že služba synchronizace úložiště a účty úložiště, které mají synchronizované sdílené složky, by měly být vždy umístěny v rámci stejného předplatného. Podporují se tyto kombinace:

* Služba synchronizace úložiště a účty úložiště se nacházejí v **různých skupinách prostředků** (stejný tenant Azure).
* Služba synchronizace úložiště a účty úložiště se nacházejí v **různých předplatných** (stejný tenant Azure).

> [!IMPORTANT]
> Prostřednictvím různých kombinací přesunů můžou služby synchronizace úložiště a účty úložiště skončit v různých předplatných, které se řídí různými klienty AAD. Synchronizace by dokonce vypadala i v případě, že se nejedná o podporovanou konfiguraci. Synchronizace se může v budoucnu zastavit bez možnosti vrátit se zpátky do funkční podmínky.

Při plánování přesunu prostředků jsou k dispozici různé předpoklady pro [Přesun v rámci stejného tenanta služby AAD](#move-within-the-same-azure-active-directory-tenant) a přesun mezi [do jiného tenanta AAD](#move-to-a-new-azure-active-directory-tenant). Při přesunu tenantů služby AAD vždycky přesunete synchronizaci a prostředky úložiště společně.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Přesunout v rámci stejného Azure Active Directory tenanta

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Obrázek znázorňující Azure Portal prostředku služby synchronizace úložiště s rozbaleným příkazem pro přesunutí Zobrazuje možnosti přesunutí skupiny prostředků a přesunu předplatného." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Vhodný způsob, jak přesunout prostředek služby synchronizace úložiště, je použít Azure Portal. Přejděte do služby synchronizace úložiště, kterou chcete přesunout, a na panelu příkazů vyberte **přesunout** . Stejný postup platí pro přesun účtu úložiště. Tímto způsobem můžete také přesunout všechny prostředky ve skupině prostředků. Přesunutí celé skupiny prostředků se doporučuje, když máte službu synchronizace úložiště a všechny její účty úložiště jsou v této skupině prostředků použité.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Když přesunete prostředek účtu úložiště, synchronizace se okamžitě zastaví. Pro přístup k příslušným účtům úložiště v novém předplatném je nutné ručně autorizovat synchronizaci. V části [ověřování přístupu k úložišti synchronizace souborů Azure](#azure-file-sync-storage-access-authorization) se zobrazí nezbytné kroky.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Přesunout na nového tenanta Azure Active Directory

Jednotlivé prostředky, jako je služba synchronizace úložiště nebo účty úložiště, se nemohou přesunout samy o sebe do jiného tenanta AAD. Jenom předplatná Azure můžou přesunout klienty AAD. Zamyslete se nad strukturou vašeho předplatného v novém tenantovi AAD. Pro Synchronizace souborů Azure můžete použít vyhrazené předplatné. 

1. Vytvořte předplatné Azure (nebo určete existující ve starém tenantovi, který by se měl přesunout.
1. [Proveďte přesun předplatného v rámci stejného TENANTA AAD](#move-within-the-same-azure-active-directory-tenant) služby synchronizace úložiště a všech přidružených účtů úložiště.
1. Synchronizace se zastaví. Dokončete svůj tenant [, aby se okamžitě přesunul nebo obnovil možnost synchronizace pro přístup k účtům úložiště, které se přesunuly](#azure-file-sync-storage-access-authorization). Později se můžete přesunout na nového tenanta AAD.

Jakmile se všechny související Synchronizace souborů Azure prostředky sequestered do svého vlastního předplatného, budete připraveni přesunout celé předplatné do cílového tenanta AAD. [Průvodce odběrem přenosu](../../role-based-access-control/transfer-subscription.md) vám umožňuje naplánovat a spustit takový přenos.

> [!WARNING]
> Když přenášíte předplatné z jednoho klienta na jiný, synchronizace se okamžitě zastaví. Pro přístup k příslušným účtům úložiště v novém předplatném je nutné ručně autorizovat synchronizaci. V části [ověřování přístupu k úložišti synchronizace souborů Azure](#azure-file-sync-storage-access-authorization) se zobrazí nezbytné kroky.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Obrázek znázorňující okno Azure Portal, přehled předplatného a zvýraznění příkazu změnit adresář panelu nástrojů v centru, horní části stránky." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Pokud máte plán a požadovaná oprávnění, jste připraveni zahájit migraci:
        1. V Azure Portal přejděte do svého předplatného, okno **Přehled** .
        1. Vyberte **Změnit adresář**.
        1. Podle pokynů Průvodce přiřaďte nového tenanta AAD.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Synchronizace souborů Azure autorizaci přístupu k úložišti

Když se účty úložiště přesunou buď do nového předplatného, nebo se přesunou v rámci předplatného na nového tenanta Azure Active Directory (AAD), synchronizace se zastaví. Přístup založený na rolích (RBAC) slouží k autorizaci Synchronizace souborů Azure k přístupu k účtu úložiště a Tato přiřazení rolí se s prostředky nemigrují.

### <a name="azure-file-sync-service-principal"></a>Synchronizace souborů Azure instanční objekt

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Obrázek znázorňující Azure Portal, správu předplatných a registrovaných poskytovatelů prostředků." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        Aby bylo možné autorizovat přístup k účtu úložiště, musí v tenantovi služby AAD existovat Synchronizace souborů Azure instanční objekt. </br></br> Když v současnosti vytvoříte nové předplatné Azure, Synchronizace souborů Azure poskytovatel prostředků *Microsoft. StorageSync* se automaticky zaregistruje u vašeho předplatného. Registrace poskytovatele prostředků vytvoří *instanční objekt* , který bude k dispozici pro synchronizaci, v klientovi Azure Active Directory, který řídí předplatné. Instanční objekt je podobný uživatelskému účtu ve službě AAD. K autorizaci přístupu k prostředkům prostřednictvím řízení přístupu na základě role (RBAC) můžete použít Synchronizace souborů Azure instanční objekt. Jediná synchronizace prostředků potřebuje přístup k účtům úložiště, které obsahují sdílené složky, které se mají synchronizovat. *Microsoft. StorageSync* se musí přiřadit k předdefinované **čtečce rolí a přístup k datům** v účtu úložiště. </br></br> Toto přiřazení se provádí automaticky prostřednictvím uživatelského kontextu přihlášeného uživatele, když přidáte sdílenou složku do skupiny synchronizace nebo jinými slovy, vytvoříte koncový bod cloudu. Když se účet úložiště přesune do nového předplatného nebo do tenanta AAD, přiřazení této role se ztratí a [musí se ručně znovu vytvořit](#establish-sync-access-to-a-storage-account).
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Pokud se cílové předplatné Azure v poslední době nevytvořilo, ověřte, jestli je v předplatném zaregistrovaný poskytovatel prostředků *Microsoft. StorageSync* . Pokud ne, ručně ho přidejte do okna stejného portálu.

### <a name="establish-sync-access-to-a-storage-account"></a>Vytvoření přístupu pro synchronizaci k účtu úložiště

K autorizaci přístupu k účtu úložiště prostřednictvím řízení přístupu na základě role (RBAC) se musí použít [objekt služby synchronizace souborů Azure](#azure-file-sync-service-principal) . *Microsoft. StorageSync* se musí přiřadit k předdefinované **čtečce rolí a přístup k datům** v účtu úložiště. 

Toto přiřazení se obvykle provádí automaticky prostřednictvím uživatelského kontextu přihlášeného uživatele, když přidáte sdílenou složku do skupiny synchronizace nebo jinými slovy, vytvoříte koncový bod cloudu. Pokud se ale účet úložiště přesune do nového předplatného nebo tenanta AAD, toto přiřazení role se ztratí a musí se ručně znovu vytvořit.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Obrázek zobrazující instanční objekt Microsoft. StorageSync přiřazený ke čtenářům a roli přístupu k datům v účtu úložiště":::
    :::column-end:::
    :::column:::
        V Azure Portal přejděte k účtu úložiště, ke kterému potřebujete znovu autorizovat přístup pro synchronizaci. <ol><li>V obsahu na levé straně vyberte **řízení přístupu (IAM)** .</li><li>Vyberte kartu přiřazení rolí a seznam uživatelů a aplikací (instančních objektů), které mají přístup k vašemu účtu úložiště.</li><li>Vyberte **Přidat**.</li><li>V **poli role** vyberte **Čtenář a přístup k datům**.</li><li>Do **pole vyberte** zadejte *Microsoft. StorageSync*, vyberte roli a klikněte na **Uložit**.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Přesunout do jiné oblasti Azure

*Služba synchronizace úložiště* prostředků synchronizace souborů Azure a účty úložiště obsahující sdílené složky, které se synchronizují, mají oblast Azure, ve které se nasazují. Tuto oblast určíte při vytváření prostředku. Oblast prostředků služby synchronizace úložiště a účtu úložiště se musí shodovat. Tyto oblasti nelze po vytvoření změnit u žádného typu prostředku.

Přiřazení jiné oblasti prostředku se liší od [převzetí služeb při selhání v oblasti](#region-fail-over), která může být podporovaná v závislosti na nastavení redundance účtu úložiště.

## <a name="region-fail-over"></a>Převzetí služeb při selhání oblasti

[Azure Storage nabízí možnosti geografické redundance](../common/storage-redundancy.md#geo-redundant-storage) pro účet úložiště. Tyto možnosti redundance můžou představovat problémy s účty úložiště, které se používají s Synchronizace souborů Azure. Hlavním důvodem je, že replikace mezi geograficky vzdálenými oblastmi není prováděna Synchronizace souborů Azure, ale technologií replikace úložiště integrovaným do subsystému úložiště v Azure. Nemůže pochopit stav aplikace a Synchronizace souborů Azure je aplikace se soubory, které se synchronizují do sdílených složek Azure a z nich, v daném okamžiku. Pokud se rozhodnete pro některé z těchto geograficky vydaných možností redundance úložiště, nepřijdete o všechna vaše data ve velkém rozsahu. Je však nutné [odhadnout ztrátu dat](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss).

> [!CAUTION]
> Převzetí služeb při selhání není nikdy odpovídající náhradou za zřizování vašich prostředků ve správné oblasti Azure. Pokud jsou vaše prostředky v oblasti "špatné", je nutné zvážit opětovné zastavení synchronizace a nastavení synchronizace do nových sdílených složek Azure, které jsou nasazeny v požadované oblasti.

Regionální převzetí služeb při selhání může Microsoft zahájit v závažné události, která bude vykreslovat datová centra v oblasti Azure incapacitated po delší dobu. Definice výpadků, které může vaše firma tolerovat, může být menší než čas, kdy je společnost Microsoft připravená předat před zahájením regionálního převzetí služeb při selhání. V takové situaci [můžou zákazníci iniciovat také převzetí služeb při selhání](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> V případě převzetí služeb při selhání je potřeba, abyste si pomohli odeslat lístek podpory pro vaše ovlivněné služby synchronizace úložiště, aby synchronizace mohla znovu fungovat.

## <a name="see-also"></a>Viz také

- [Přehled příruček pro migraci sdílených složek a synchronizací souborů Azure](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- [Řešit problémy se Synchronizací souborů Azure](file-sync-troubleshoot.md)
- [Plánování nasazení Synchronizace souborů Azure](file-sync-planning.md)