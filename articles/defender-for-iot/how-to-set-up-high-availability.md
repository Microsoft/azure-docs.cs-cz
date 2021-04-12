---
title: Nastavení vysoké dostupnosti
description: Tím, že nainstalujete místní zařízení s vysokou dostupností konzoly pro správu, Zvyšte odolnost vašeho Defenderu pro nasazení IoT. Nasazení s vysokou dostupností zajišťuje, že spravované senzory budou průběžně hlásit aktivní místní konzole pro správu.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: d0e09cd37fbae91d1903ca8f175c0592b567da6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781649"
---
# <a name="about-high-availability"></a>O vysoké dostupnosti

Tím, že nainstalujete místní zařízení s vysokou dostupností konzoly pro správu, Zvyšte odolnost vašeho Defenderu pro nasazení IoT. Nasazení s vysokou dostupností zajišťuje, že spravované senzory budou průběžně hlásit aktivní místní konzole pro správu.

Toto nasazení je implementováno s dvojicí místní konzoly pro správu, která zahrnuje primární a sekundární zařízení.

## <a name="about-primary-and-secondary-communication"></a>O primární a sekundární komunikaci

Při párování primární a sekundární místní konzoly pro správu:

- Místní Konzola pro správu SSL certifikát se používá k vytvoření zabezpečeného připojení mezi primárním a sekundárním zařízením. SSL může být certifikát podepsaný svým držitelem, který je nainstalovaný ve výchozím nastavení, nebo certifikát nainstalovaný zákazníkem.

- Data primární místní konzoly pro správu se automaticky zálohují do sekundární místní konzoly pro správu každých 10 minut. Konfigurace místních konzol pro správu a data zařízení se zálohují. Soubory a protokoly PCAP nejsou zahrnuté do zálohy. PCAPs a protokoly můžete zálohovat a obnovovat ručně.

- Primární instalace v konzole pro správu je duplikována v sekundárním umístění. například nastavení systému. Pokud jsou tato nastavení aktualizována na primárním počítači, aktualizují se také na sekundárním počítači.

- Před tím, než vyprší platnost licence na sekundární, byste ji měli definovat jako primární, aby bylo možné licenci aktualizovat.

## <a name="about-failover-and-failback"></a>Informace o převzetí služeb při selhání a navrácení služeb po obnovení

Pokud se senzor nemůže připojit k primární místní konzole pro správu, automaticky se připojí k sekundárnímu. Váš systém bude podporovaný současně primární i sekundární, pokud je méně než polovina senzorů komunikuje se sekundárním. Sekundární převezme více než polovinu senzorů, které s ním komunikují. Převzetí služeb při selhání z primární na sekundární trvá přibližně tři minuty. Když dojde k převzetí služeb při selhání, místní Konzola pro správu se zablokuje. Pokud k tomu dojde, můžete se přihlásit k sekundárnímu pomocí stejných přihlašovacích údajů pro přihlášení.

Během převzetí služeb při selhání se senzory stále snaží komunikovat s primárním zařízením. Pokud se s primárním senzorem úspěšně komunikuje více než polovina spravovaných senzorů, obnoví se primární. Následující zpráva se zobrazí v sekundární konzole při obnovení primární databáze.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="Snímek obrazovky zprávy, která se zobrazí v sekundární konzole při obnovení primární databáze.":::

Po přesměrování se znovu přihlaste k primárnímu zařízení.

## <a name="high-availability-setup-overview"></a>Přehled nastavení vysoké dostupnosti

Postupy instalace a konfigurace se provádějí ve čtyřech hlavních fázích:

1. Nainstalujte primární zařízení místní konzoly pro správu. 

2. Nakonfigurujte primární zařízení místní konzoly pro správu. Například nastavení naplánovaného zálohování, nastavení sítě VLAN. Podrobnosti najdete v uživatelské příručce k místní konzole pro správu. Všechna nastavení se po párování automaticky aplikují na sekundární zařízení.

3. Nainstalujte sekundární zařízení místní konzoly pro správu. Další informace najdete v tématu [o instalaci Defenderu pro IoT](how-to-install-software.md).

4. Párování primárních a sekundárních zařízení místní konzoly pro správu, jak je popsáno [zde](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console). Hlavní místní Konzola pro správu musí spravovat alespoň dva senzory, aby bylo možné provést nastavení.

## <a name="high-availability-requirements"></a>Požadavky na vysokou dostupnost

Ověřte, že jste splnili následující požadavky na vysokou dostupnost:

- Požadavky na certifikáty

- Požadavky na software a hardware

- Požadavky na přístup k síti

### <a name="software-and-hardware-requirements"></a>Požadavky na software a hardware

- Na zařízeních primární i sekundární místní konzoly pro správu musí běžet stejné hardwarové modely a verze softwaru.

- Systém s vysokou dostupností může nastavit program Defender pouze pro uživatele IoT pomocí nástrojů rozhraní příkazového řádku.

### <a name="network-access-requirements"></a>Požadavky na přístup k síti

Musíte ověřit, že vaše zásady zabezpečení vaší organizace vám umožní přístup k následujícím službám v primární a sekundární místní konzole pro správu. Tyto služby také umožňují propojení mezi senzory a sekundární místní konzolou pro správu:

|Port|Služba|Popis|
|----|-------|-----------|
|**443 nebo TCP**|HTTPS|Udělí přístup k místní webové konzoli konzoly pro správu.|
|**22 nebo TCP**|SSH|Synchronizuje data mezi primárním a sekundárním zařízením místní konzoly pro správu.|
|**123 nebo UDP**|NTP| Synchronizace času NTP místní konzoly pro správu. Ověřte, že aktivní a pasivní zařízení jsou definovaná se stejným časovém pásmem.|

## <a name="create-the-primary-and-secondary-pair"></a>Vytvoření primárního a sekundárního páru

Před zahájením postupu ověřte, zda jsou na primární i sekundární konzole pro správu na místním zařízení zapnutá.  

### <a name="on-the-primary"></a>Na primární

1. Přihlaste se ke CLI jako Defender pro uživatele IoT.

2. Na primárním počítači spusťte následující příkaz:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>V tomto dokumentu se hlavní místní Konzola pro správu označuje jako primární a Agent se označuje jako sekundární.

3. Do pole zadejte IP adresu sekundárního zařízení ```<Secondary ip>``` a vyberte zadat. IP adresa se pak ověří a certifikát SSL se stáhne na primární. Když zadáte IP adresu, připojíte senzory k sekundárnímu zařízení.

4. Spuštěním následujícího příkazu na primárním počítači ověřte, zda je certifikát správně nainstalován:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. Spusťte na primárním počítači následující příkaz. **Nespouštějte s sudo.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

To umožňuje propojení mezi primárními a sekundárními zařízeními pro účely zálohování a obnovení mezi nimi.

6. Zadejte IP adresu sekundárního a vyberte Enter.

### <a name="on-the-secondary"></a>Sekundární

1. Přihlaste se ke CLI jako Defender pro uživatele IoT.

2. Na sekundárním počítači spusťte následující příkaz. **Nespouštějte s sudo**:

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

To umožňuje propojení mezi primárními a sekundárními zařízeními pro účely zálohování a obnovy mezi nimi.

3. Zadejte IP adresu primárního primárního a stiskněte klávesu ENTER.

### <a name="track-high-availability-activity"></a>Aktivita sledování vysoké dostupnosti

Základní protokoly aplikací je možné exportovat do programu Defender pro tým podpory IoT, který bude zpracovávat problémy s vysokou dostupností.  

Přístup k základním protokolům:

1. V okně **nastavení systému** vyberte **exportovat** .

## <a name="update-the-on-premises-management-console-with-high-availability"></a>Aktualizace místní konzoly pro správu s vysokou dostupností

Proveďte aktualizaci vysoké dostupnosti v uvedeném pořadí. Než začnete s novým krokem, ujistěte se, že je každý krok dokončen.

Aktualizace s vysokou dostupností:

1. Aktualizujte primární místní konzolu pro správu.

2. Aktualizujte sekundární místní konzolu pro správu.

3. Aktualizujte senzory.

## <a name="see-also"></a>Viz také

[Aktivace a nastavení místní konzoly pro správu](how-to-activate-and-set-up-your-on-premises-management-console.md)