---
title: Rozšíření stávajícího fondu hostitelů o nové hostitele relací – Azure
description: Jak rozšířit existující fond hostitelů s novými hostiteli relací ve virtuální ploše windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aee5195fe86fed3e631908a38d3bdb7d5e4883b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365215"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Rozšíření existujícího fondu hostitelů s novými hostiteli relací

Při navyšování využití v rámci fondu hostitelů může být nutné rozšířit stávající fond hostitelů o nové hostitele pro zpracování nového zatížení.

V tomto článku se zobrazí způsob, jakým můžete rozšířit existující fond hostitelů s novými hostiteli relací.

## <a name="what-you-need-to-expand-the-host-pool"></a>Co potřebujete k rozšíření hostitelského fondu

Než začnete, ujistěte se, že jste vytvořili fond hostitelů a virtuální počítače hostitele relací pomocí jedné z následujících metod:

- [Nabídka Azure Marketplace](./create-host-pools-azure-marketplace.md)
- [Šablona GitHub Azure Resource Manager](./create-host-pools-arm-template.md)
- [Vytvoření fondu hostitelů pomocí PowerShellu](./create-host-pools-powershell.md)

Budete také potřebovat následující informace z při prvním vytvoření fondu hostitelů a virtuálních hostitelských virtuálních aplikací pro relace:

- Předpona velikosti virtuálního počítače, obrázku a názvu
- Připojení k doméně a pověření správce klienta Virtuální plochy Windows
- Název virtuální sítě a podsítě

Další tři části jsou tři metody, které můžete použít k rozšíření fondu hostitelů. Můžete to udělat buď s tím, co nasazení nástroj, který vám vyhovuje.

>[!NOTE]
>Během fáze nasazení se zobrazí chybové zprávy pro prostředky hlavního hostitele relace, pokud jsou aktuálně vypnuté. K těmto chybám dochází, protože Azure nelze spustit rozšíření PowerShell DSC k ověření, že virtuální počítače hostitele relace jsou správně zaregistrovány do vašeho stávajícího fondu hostitelů. Můžete bezpečně ignorovat tyto chyby, nebo můžete vyhnout chybám spuštěním všech virtuálních her hostitele relace v existujícím fondu hostitelů před zahájením procesu nasazení.

## <a name="redeploy-from-azure"></a>Opětovné nasazení z Azure

Pokud jste už vytvořili fond hostitelů a virtuální počítače hostitele relací pomocí [nabídky Azure Marketplace](./create-host-pools-azure-marketplace.md) nebo šablony [GitHub Azure Resource Manager](./create-host-pools-arm-template.md), můžete znovu nasadit stejnou šablonu z webu Azure Portal. Opětovné nasazení šablony automaticky znovu zadá všechny informace, které jste zadali do původní šablony s výjimkou hesel.

Tady je postup, jak znovu nasadit šablonu Azure Resource Manageru a rozbalit fond hostitelů:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Na panelu hledání v horní části portálu Azure vyhledejte **skupiny prostředků** a vyberte položku v části **Služby**.
3. Najděte a vyberte skupinu prostředků, kterou jste vytvořili při vytváření fondu hostitelů.
4. V panelu na levé straně prohlížeče vyberte **Možnost Nasazení**.
5. Vyberte vhodné nasazení pro proces vytváření fondu hostitelů:
     - Pokud jste vytvořili původní fond hostitelů s nabídkou Azure Marketplace, vyberte nasazení počínaje **rds.wvd-provision-host-pool**.
     - Pokud jste vytvořili původní fond hostitelů pomocí šablony GitHub Azure Resource Manager, vyberte nasazení s názvem **Microsoft.Template**.
6. Vyberte **možnost Znovu nasadit**.
     
     >[!NOTE]
     >Pokud se šablona při výběru **možnosti Znovu nasadit**automaticky znovu nenasadí , vyberte **šablonu** v panelu na levé straně prohlížeče a pak vyberte **Nasadit**.

7. Vyberte skupinu prostředků, která obsahuje aktuální virtuální servery hostitele relace v existujícím fondu hostitelů.
     
     >[!NOTE]
     >Pokud se zobrazí chyba, která vás informuje o výběru jiné skupiny prostředků, i když je zadaná skupina správných, vyberte jinou skupinu prostředků a vyberte původní skupinu prostředků.

8. Zadejte následující adresu URL *_artifactsLocation*:`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Do pole *Rdsh Počet instancí*zadejte požadovaný nový celkový počet hostitelů relací . Pokud například rozšiřujete fond hostitelů z pěti hostitelů relací na osm, zadejte **8**.
10. Zadejte stejné existující heslo domény, které jste použili pro existující hlavní název domény. Neměňte uživatelské jméno, protože to způsobí chybu při spuštění šablony.
11. Zadejte stejné heslo správce klienta, které jste použili pro ID uživatele nebo aplikace, které jste zadali pro *ID správce klienta nebo ID aplikace*. Opět neměňte uživatelské jméno.
12. Dokončete odeslání a rozšiřte svůj hostitelský fond.

## <a name="run-the-azure-marketplace-offering"></a>Spuštění nabídky Azure Marketplace

Postupujte podle pokynů v [části Vytvoření fondu hostitelů pomocí Azure Marketplace,](./create-host-pools-azure-marketplace.md) dokud se nedostanete [do nabídky Azure Marketplace a nezřídíte nový fond hostitelů](./create-host-pools-azure-marketplace.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool). Když se dostanete k tomuto bodu, budete muset zadat následující informace pro každou kartu:

### <a name="basics"></a>Základy

Všechny hodnoty v této části by měly odpovídat tomu, co jste zadali při prvním vytvoření fondu hostitelů a hostitelských virtuálních počítačů relací, s výjimkou *výchozích uživatelů plochy*:

1.    V *části Předplatné*vyberte předplatné, ve kterém jste fond hostitelů vytvořili jako první.
2.    Pro *skupinu prostředků*vyberte stejnou skupinu prostředků, ve které jsou umístěny existující virtuální virtuální soudy hostitelských relací hostitelského fondu.
3.    For *Region*, select the same region where the existing host pool session host VMs are located.
4.    Do *pole Název hostpoolu*zadejte název existujícího fondu hostitelů.
5.    V *části Typ plocha*vyberte typ plochy, který odpovídá existujícímu fondu hostitelů.
6.    U *výchozích uživatelů stolních počítačů*zadejte seznam odlišených čárkami všech dalších uživatelů, kteří se chtějí přihlásit ke klientům Virtuální plochy Windows a po dokončení nabídky Azure Marketplace přistupovat k ploše. Chcete-li například přiřadit user3@contoso.com user4@contoso.com a získat user3@contoso.comuser4@contoso.compřístup, zadejte . .
7.    Vyberte **další : Konfigurace virtuálního počítače**.

>[!NOTE]
>S výjimkou *výchozích uživatelů plochy*musí všechna pole přesně odpovídat tomu, co bylo nakonfigurováno v existujícím fondu hostitelů. Pokud je neshoda, která bude mít za následek nový fond hostitelů.

### <a name="configure-virtual-machines"></a>Konfigurace virtuálních počítačů

Všechny hodnoty parametrů v této části by měly odpovídat tomu, co jste zadali při prvním vytvoření fondu hostitelů a hostitelských virtuálních hostitelských virtuálních her, s výjimkou celkového počtu virtuálních her. Počet virtuálních počítačů, které zadáte, bude počet virtuálních počítačů v rozšířeném fondu hostitelů:

1. Vyberte velikost virtuálního počítače, která odpovídá existujícím hostitelským virtuálním počítačem relace.
    
    >[!NOTE]
    >Pokud se konkrétní velikost virtuálního počítače, kterou hledáte, nezobrazuje ve voliči velikosti virtuálních počítače, je to proto, že jsme ji ještě nezaločili do nástroje Azure Marketplace. Chcete-li požádat o velikost virtuálního počítače, vytvořte požadavek nebo přehlaste existující požadavek ve [fóru Windows Virtual Desktop UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Přizpůsobte *profil využití*, *celkový počet uživatelů*a počet parametrů *virtuálních počítačů* a vyberte celkový počet hostitelů relací, které chcete mít ve fondu hostitelů. Pokud například rozšiřujete fond hostitelů z pěti hostitelů relací na osm, nakonfigurujte tyto možnosti tak, aby se dostali k 8 virtuálním počítačům.
3. Zadejte předponu pro názvy virtuálních počítačů. Pokud například zadáte název "prefix", virtuální počítače se budou nazývat "prefix-0", "prefix-1" a tak dále.
4. Vyberte **další : Nastavení virtuálního počítače**.

### <a name="virtual-machine-settings"></a>Nastavení virtuálního počítače

Všechny hodnoty parametrů v této části by měly odpovídat tomu, co jste zadali při prvním vytvoření fondu hostitelů a virtuálních hostitelských virtuálních aplikací hostitele relace:

1. Pro *zdroj obrazu* a verzi *operačního systému image*zadejte stejné informace, které jste zadali při prvním vytvoření fondu hostitelů.
2. Pro *doménu služby AD připojit upn* a související hesla, zadejte stejné informace, které jste zadali při prvním vytvoření fondu hostitelů pro připojení virtuálních počítače do domény služby Active Directory. Tato pověření se použijí k vytvoření místního účtu ve virtuálních počítačích. Chcete-li později změnit jejich přihlašovací údaje, můžete tyto místní účty obnovit.
3. For the virtual network information, select the same virtual network and subnet for where your existing host pool session host VMs are located.
4. Vyberte **další : Konfigurace informací o virtuální ploše systému Windows**.

### <a name="windows-virtual-desktop-information"></a>Informace o virtuální ploše systému Windows

Všechny hodnoty parametrů v této části by měly odpovídat tomu, co jste zadali při prvním vytvoření fondu hostitelů a virtuálních hostitelských virtuálních aplikací hostitele relace:

1. Pro *název skupiny klienta Virtuální plocha Windows*zadejte název skupiny klientů, která obsahuje vašeho klienta. Ponechte ji jako výchozí, pokud jste zadali konkrétní název skupiny klientů.
2. Pro *název klienta Virtuální plochy Windows*zadejte název klienta, ve kterém budete vytvářet tento fond hostitelů.
3. Zadejte stejná pověření, která jste použili při prvním vytvoření fondu hostitelů a virtuálních hostitelských virtuálních hostitelů relací. Pokud používáte instanční objekt, zadejte ID instance služby Azure Active Directory, kde je umístěn instanční objekt služby.
4. Vyberte **další : Kontrola + vytvoření**.

## <a name="run-the-github-azure-resource-manager-template"></a>Spuštění šablony Správce prostředků Azure githubu

Postupujte podle pokynů v [spuštění šablony Azure Resource Manager pro zřizování nového fondu hostitelů](./create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) a zadejte všechny stejné hodnoty parametrů s výjimkou *rdsh počet instancí*. Zadejte počet hostitelských virtuálních počítače relace, které chcete v hostitelském fondu po spuštění šablony. Pokud například rozšiřujete fond hostitelů z pěti hostitelů relací na osm, zadejte **8**.

## <a name="next-steps"></a>Další kroky

Teď, když jste rozšířili stávající fond hostitelů, můžete se přihlásit ke klientovi virtuální plochy Windows a otestovat je jako součást uživatelské relace. K relaci se můžete připojit s libovolným z následujících klientů:

- [Připojení s desktopovým klientem Windows](./connect-windows-7-and-10.md)
- [Připojení k webovému klientovi](./connect-web.md)
- [Práce s klientem Android](./connect-android.md)
- [Připojení s klientem macOS](./connect-macos.md)
- [Připojení s klientem iOS](./connect-ios.md)
