---
title: Diagnostika problémů s konfigurací privátních propojení na Azure Key Vault
description: Řešení běžných problémů s privátními odkazy pomocí Key Vault a hluboko podrobně do konfigurace
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 52ac5b89a0c7173b9b2585f84b5f34361b4b136c
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744215"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Diagnostika problémů s konfigurací privátních propojení na Azure Key Vault

## <a name="introduction"></a>Úvod

Tento článek pomáhá uživatelům diagnostikovat a opravit problémy týkající se Key Vault a funkce privátních odkazů. Tato příručka vám pomůže s aspekty konfigurace, jako je například získání privátních odkazů pracujících poprvé, nebo pro opravu situace, kdy privátní odkazy přestaly fungovat z důvodu nějaké změny.

Pokud s touto funkcí začínáte, přečtěte si téma [integrace Key Vault s privátním odkazem Azure](private-link-service.md).

### <a name="problems-covered-by-this-article"></a>Problémy, na které se vztahuje tento článek

- Vaše dotazy DNS pořád vrátí veřejnou IP adresu pro Trezor klíčů místo na privátní IP adresu, kterou byste očekávali od použití funkce privátních odkazů.
- Všechny požadavky prováděné daným klientem, který používá privátní propojení, selžou s časovými prodlevami nebo chybami sítě a problém není přerušovaný.
- Trezor klíčů má soukromou IP adresu, ale požadavky pořád obdrží `403` odpověď s `ForbiddenByFirewall` kódem vnitřní chyby.
- Používáte privátní odkazy, ale váš Trezor klíčů stále přijímá žádosti z veřejného Internetu.
- Váš Trezor klíčů má dva privátní koncové body. Žádosti používající jednu z nich fungují správně, ale požadavky na jiné selžou.
- Máte jiné předplatné, Trezor klíčů nebo virtuální síť, které používají privátní odkazy. Chcete vytvořit nové podobné nasazení, ale nemůžete získat privátní odkazy na práci.

### <a name="problems-not-covered-by-this-article"></a>Problémy, na které se nevztahuje tento článek

- Dochází k přerušovanému problému s připojením. V daném klientovi vidíte, že některé žádosti fungují a některé nefungují. *Občasné problémy většinou nejsou způsobené problémem v konfiguraci privátních odkazů. Jedná se o znaménko přetížení sítě nebo klienta.*
- Používáte produkt Azure, který podporuje BYOK (Bring Your Own Key) nebo CMK (klíče spravované zákazníkem) a tento produkt nemá přístup k vašemu trezoru klíčů. *Podívejte se na dokumentaci k produktu. Ujistěte se, že explicitně nastavují podporu pro trezory klíčů s povolenou bránou firewall. V případě potřeby kontaktujte produktovou podporu pro daný produkt.*

### <a name="how-to-read-this-article"></a>Postup čtení tohoto článku

Pokud se nejedná o privátní odkazy nebo jste si vyhodnotili složité nasazení, doporučujeme si přečíst celý článek. V opačném případě si můžete vybrat oddíl, který je pro problém, na který se chystáte, smysluplnější.

Pusťme se do toho.

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. Potvrďte, že jste vlastní připojení klienta.

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>Potvrďte, že váš klient běží ve virtuální síti.

Tato příručka je určená k tomu, aby vám pomohla při určování připojení k trezoru klíčů, který pochází z kódu aplikace. Příklady jsou aplikace a skripty spouštěné v Azure Virtual Machines, clusterech Azure Service Fabric, Azure App Service, služba Azure Kubernetes (AKS) a podobné jiné.

V rámci definice privátních odkazů musí být aplikace nebo skript spuštěné v počítači, clusteru nebo prostředí připojeném k Virtual Network, kde byl nasazen [prostředek privátního koncového bodu](../../private-link/private-endpoint-overview.md) . Pokud je aplikace spuštěná v libovolné síti připojené k Internetu, tato příručka se nedá použít a pravděpodobně se nedají použít privátní odkazy.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>Pokud používáte spravované řešení, přečtěte si konkrétní dokumentaci.

Tato příručka se nevztahuje na řešení spravovaná Microsoftem, kde je k trezoru klíčů přistupující produkt Azure, který existuje nezávisle na Virtual Network zákazníka. Příklady takových scénářů jsou Azure Storage nebo Azure SQL nakonfigurovaný pro šifrování v klidovém centru událostí Azure, které šifruje data pomocí klíčů poskytnutých zákazníky, Azure Data Factory přístup k přihlašovacím údajům služby uloženým v trezoru klíčů, Azure Pipelines načítání tajných kódů z trezoru klíčů a dalších podobných scénářích. V těchto případech je *nutné ověřit, zda produkt podporuje trezory klíčů s povolenou bránou firewall*. Tato podpora se obvykle provádí s funkcí [důvěryhodné služby](overview-vnet-service-endpoints.md#trusted-services) Key Vault brány firewall. Řada produktů však není v seznamu důvěryhodných služeb obsažena z nejrůznějších důvodů. V takovém případě se dostanou k podpoře pro konkrétní produkt.

Malý počet produktů Azure podporuje koncept *vkládání virtuální*sítě. V jednoduchých případech produkt přidá síťové zařízení do Virtual Network zákazníka, což umožňuje odesílat žádosti, jako kdyby byla nasazená do Virtual Network. [Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)je významným příkladem. Tyto produkty můžou pomocí privátních odkazů dělat požadavky na Trezor klíčů a tato příručka pro odstraňování potíží vám může pomoci.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. Potvrďte, že připojení bylo schváleno a bylo úspěšné.

Následující kroky ověřují, zda je připojení privátního koncového bodu schváleno a úspěšné:

1. Otevřete Azure Portal a otevřete prostředek trezoru klíčů.
2. V nabídce vlevo vyberte **síť**.
3. Klikněte na kartu **připojení privátního koncového bodu** . Zobrazí se všechna připojení privátního koncového bodu a jejich příslušné stavy. Pokud neexistují žádná připojení nebo pokud chybí připojení k vašemu Virtual Network, je nutné vytvořit nový privátní koncový bod. Tato akce bude zahrnuta později.
4. Pořád jste v **připojeních privátních koncových bodů**našli tu, kterou diagnostikuje, a ověříte, že stav připojení je **schválený** a že je stav zřizování **úspěšný**.
    - Pokud je připojení ve stavu čeká na vyřízení, může být možné ho jenom schválit.
    - Pokud se nejedná o připojení "zamítnuto", "neúspěšné", "Chyba", "Odpojeno" nebo jiný stav, je nutné vytvořit nový prostředek privátního koncového bodu.

Je vhodné odstranit neúčinná připojení, aby se zajistilo, že bude něco vyčistit.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. Zkontrolujte, jestli je brána firewall trezoru klíčů správně nakonfigurovaná.

>[!IMPORTANT]
> Změna nastavení brány firewall může odebrat přístup z legitimních klientů, kteří stále nepoužívají privátní odkazy. Ujistěte se, že jste si vědomi dopadů každé změny v konfiguraci brány firewall.

Důležitým pojmem je, že soukromé odkazy *poskytují* přístup pouze k vašemu trezoru klíčů. Neodebere žádný *remove* existující přístup. Aby bylo možné efektivně blokovat přístup z veřejného Internetu, je nutné povolit bránu firewall trezoru klíčů explicitně:

1. Otevřete Azure Portal a otevřete prostředek trezoru klíčů.
2. V nabídce vlevo vyberte **síť**.
3. Ujistěte se, že je vybraná karta **brány firewall a virtuální sítě** nahoře.
4. Ujistěte se, že je vybraná možnost **privátní koncový bod a vybrané sítě** . Pokud vyhledáte **všechny sítě** , vysvětlují, proč mají externí klienti stále přístup k trezoru klíčů.

Následující příkazy platí také pro nastavení brány firewall:

- Funkce privátních odkazů nevyžaduje, aby se v nastavení brány firewall trezoru klíčů zadala žádná "virtuální síť". Všechny požadavky, které používají privátní IP adresu trezoru klíčů (viz další oddíl), musí fungovat, i když v nastavení brány firewall trezoru klíčů není zadaná žádná virtuální síť.
- Funkce privátních odkazů nevyžaduje zadání IP adresy v nastavení brány firewall trezoru klíčů. Všechny požadavky, které používají privátní IP adresu trezoru klíčů, musí fungovat i v případě, že v nastavení brány firewall nebyla zadána žádná IP adresa.

Používáte-li privátní odkazy, jsou jedinou motivací pro zadání virtuální sítě nebo IP adresy v bráně firewall trezoru klíčů tyto funkce:

- Máte hybridní systém, ve kterém někteří klienti používají privátní odkazy, některé koncové body služby používají, některé veřejné IP adresy.
- Provádíte přechod na soukromé odkazy. V takovém případě, Jakmile ověříte, že všichni klienti používají privátní odkazy, měli byste z nastavení brány firewall trezoru klíčů odebrat virtuální sítě a IP adresy.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. Zajistěte, aby měl Trezor klíčů privátní IP adresu.

### <a name="difference-between-private-and-public-ip-addresses"></a>Rozdíl mezi privátními a veřejnými IP adresami

Privátní IP adresa má vždy jeden z následujících formátů:

- 10. x. x. x: příklady: `10.1.2.3` , `10.56.34.12` .
- 172.16. x. x na 172.32. x. x: příklady: `172.20.1.1` , `172.31.67.89` .
- 192.168. x. x: příklady: `192.168.0.1` , `192.168.100.7`

Některé IP adresy a rozsahy jsou rezervované:

- 224. x. x. x: vícesměrové vysílání
- 255.255.255.255: všesměrové vysílání
- 127. x. x. x: zpětná smyčka
- 169.254. x. x: propojení – místní
- 168.63.129.16: interní DNS

Všechny ostatní IP adresy jsou veřejné.

Když procházíte portál nebo spustíte příkaz, který zobrazuje IP adresu, ujistěte se, že můžete zjistit, jestli je tato IP adresa soukromá, veřejná nebo rezervovaná. Aby privátní odkazy fungovaly, musí se název hostitele trezoru klíčů přeložit na privátní IP adresu, která patří do Virtual Network adresního prostoru.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>Najít soukromou IP adresu trezoru klíčů ve virtuální síti

Budete muset diagnostikovat překlad názvů hostitelů a pro to, aby bylo nutné znát přesnou privátní IP adresu vašeho trezoru klíčů s povolenými soukromými odkazy. Chcete-li zjistit tuto adresu, postupujte podle tohoto postupu:

1. Otevřete Azure Portal a otevřete prostředek trezoru klíčů.
2. V nabídce vlevo vyberte **síť**.
3. Klikněte na kartu **připojení privátního koncového bodu** . Zobrazí se všechna připojení privátního koncového bodu a jejich příslušné stavy.
4. Najděte tu, kterou diagnostikuje, a ověřte, že stav připojení je **schválený** a že stav zřizování je **úspěšný**. Pokud se to vám nezobrazuje, vraťte se zpátky k předchozím částem tohoto dokumentu.
5. Po nalezení správné položky klikněte na odkaz ve sloupci **privátní koncový bod** . Tím se otevře prostředek privátního koncového bodu.
6. Na stránce s přehledem se může zobrazit oddíl s názvem **vlastní nastavení DNS**. Potvrďte, že existuje jenom jedna položka, která odpovídá názvu hostitele trezoru klíčů. Tato položka zobrazuje privátní IP adresu trezoru klíčů.
7. Můžete také kliknout na odkaz v **síťovém rozhraní** a potvrdit, že privátní IP adresa je stejná jako v předchozím kroku. Síťové rozhraní je virtuální zařízení, které představuje Trezor klíčů.

IP adresa je ta, kterou virtuální počítače a další zařízení *běžící ve stejné Virtual Network* použijí pro připojení k trezoru klíčů. Poznamenejte si IP adresu, nebo ponechte kartu prohlížeče otevřenou a při dalším vyšetřování si ji nedělejte kontaktovat.

>[!NOTE]
> Pokud má váš Trezor klíčů více privátních koncových bodů, má několik privátních IP adres. To je užitečné pouze v případě, že máte více virtuálních sítí, které přistupují ke stejnému trezoru klíčů, z nichž každý má vlastní soukromý koncový bod (soukromý koncový bod patří do jednoho Virtual Network). Ujistěte se, že jste diagnostikovat problém pro správnou Virtual Network, a v postupu výše vyberte správné připojení privátního koncového bodu. Kromě toho **nevytvářejte více** privátních koncových bodů pro stejné Key Vault ve stejném Virtual Network. To není nutné a je zdrojem nejasností.

## <a name="5-validate-the-dns-resolution"></a>5. Ověřte překlad DNS.

Překlad názvů DNS je proces překladu názvu hostitele trezoru klíčů (příklad: `fabrikam.vault.azure.net` ) na IP adresu (například: `10.1.2.3` ). Následující pododdíly znázorňují očekávané výsledky překladu názvů DNS v každém scénáři.

### <a name="key-vaults-without-private-link"></a>Trezory klíčů bez privátního propojení

Tato část je určená pro účely učení. Pokud Trezor klíčů nemá ve schváleném stavu připojení privátního koncového bodu, překlad názvu hostitele poskytne výsledek podobný tomuto:

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

Můžete vidět, že se název přeloží na veřejnou IP adresu a že neexistuje žádný `privatelink` alias. Alias se vysvětluje později, nemusíte si ho dělat hned teď.

Očekávaný výsledek je očekáván bez ohledu na to, že je počítač připojen k Virtual Network nebo se jedná o libovolný počítač s připojením k Internetu. K tomu dochází, protože Trezor klíčů nemá žádné připojení privátního koncového bodu ve schváleném stavu, a proto není nutné, aby Trezor klíčů podporoval privátní odkazy.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>Trezor klíčů s řešením privátního propojení z libovolného internetového počítače

Pokud má Trezor klíčů jedno nebo více připojení privátních koncových bodů ve schváleném stavu a překládáte název hostitele z libovolného počítače připojeného k Internetu ( *počítač, který není připojen* k Virtual Network, kde se nachází soukromý koncový bod), najdete toto:

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

Významný rozdíl oproti předchozímu scénáři spočívá v tom, že je k dispozici nový alias s hodnotou `{vaultname}.privatelink.vaultcore.azure.net` . To znamená, že rovina dat trezoru klíčů je připravena přijímat požadavky z privátních odkazů.

Neznamená to, že požadavky prováděné z počítačů *mimo* Virtual Network (jako je ta, kterou jste právě použili), budou používat privátní odkazy – nebudou. Můžete vidět, že se název hostitele pořád překládá na veřejnou IP adresu. Privátní odkazy můžou používat jenom počítače *připojené k Virtual Network* . V takovém případě se bude postupovat.

Pokud alias nevidíte `privatelink` , znamená to, že Trezor klíčů má ve stavu nenulové připojení privátního koncového bodu `Approved` . Před opakováním akce se vraťte do [této části](#2-confirm-that-the-connection-is-approved-and-succeeded) .

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Trezor klíčů s řešením privátního propojení z Virtual Network

Pokud má Trezor klíčů jedno nebo více připojení privátních koncových bodů ve schváleném stavu a překládáte název hostitele z počítače připojeného k Virtual Network, kde byl privátní koncový bod vytvořen, jedná se o očekávanou odpověď:

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  10.1.2.3
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net

Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3

Existují dva významné rozdíly. Nejprve se název přeloží na soukromou IP adresu. Musí to být IP adresa, kterou jsme našli v [odpovídající části](#find-the-key-vault-private-ip-address-in-the-virtual-network) tohoto článku. Za druhé, po jednom neexistují žádné další aliasy `privatelink` . K tomu dochází, protože servery DNS Virtual Network *zachycují* řetězec aliasů a VRACEJÍ privátní IP adresu přímo z názvu `fabrikam.privatelink.vaultcore.azure.net` . Tato položka je vlastně `A` záznamem v zóně privátní DNS. V takovém případě se bude postupovat.

>[!NOTE]
> K výše uvedenému výsledku dojde pouze ve virtuálním počítači připojeném k Virtual Network, kde byl vytvořen privátní koncový bod. Pokud nemáte nasazený virtuální počítač v Virtual Network, který obsahuje privátní koncový bod, nasaďte ho a připojte se k němu vzdáleně a pak spusťte `nslookup` příkaz (Windows) nebo `host` příkaz (Linux) výše.

Pokud spustíte tyto příkazy na virtuálním počítači připojeném k Virtual Network, kde byl vytvořen privátní koncový bod, a **nezobrazuje privátní** IP adresu trezoru klíčů, může další část pomohly problém vyřešit.

## <a name="6-validate-the-private-dns-zone"></a>6. Ověřte zónu Privátní DNS.

Pokud překlad DNS nefunguje tak, jak je popsáno v předchozí části, může se jednat o problém se zónou Privátní DNS a tato část může pomáhat. Pokud překlad DNS zobrazuje správnou privátní IP adresu trezoru klíčů, je vaše zóna Privátní DNS pravděpodobně správná. Můžete přeskočit celou část.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Potvrďte, že požadovaný prostředek zóny Privátní DNS existuje.

Vaše předplatné Azure musí mít prostředek [zóny privátní DNS](../../dns/private-dns-privatednszone.md) s tímto přesným názvem:

    privatelink.vaultcore.azure.net

Přítomnost tohoto prostředku můžete zjistit tak, že na portálu kliknete na stránku předplatné a v nabídce vlevo vyberete "prostředky". Název prostředku musí být `privatelink.vaultcore.azure.net` a typ prostředku musí být **privátní DNS zóna**.

Obvykle se tento prostředek vytvoří automaticky při vytvoření privátního koncového bodu pomocí typické metody. Existují však případy, kdy tento prostředek není vytvořen automaticky a je třeba jej provést ručně. Tento prostředek se mohl také omylem odstranit.

Pokud tento prostředek nemáte, vytvořte nový prostředek zóny Privátní DNS v rámci svého předplatného. Pamatujte, že název musí být přesně `privatelink.vaultcore.azure.net` , bez mezer nebo dalších teček. Pokud zadáte nesprávný název, řešení překladu názvů popsané v tomto článku nebude fungovat. Další informace o tom, jak vytvořit tento prostředek, najdete v tématu [Vytvoření privátní zóny DNS Azure pomocí Azure Portal](../../dns/private-dns-getstarted-portal.md). Pokud budete postupovat podle této stránky, můžete přeskočit vytváření Virtual Network, protože v tomto okamžiku byste už měli mít nějaký. Můžete také přeskočit ověřovací procedury pomocí Virtual Machines.

### <a name="confirm-that-the-private-dns-zone-must-be-linked-to-the-virtual-network"></a>Potvrďte, že zóna Privátní DNS musí být propojená s Virtual Network

Nestačí, abyste měli zónu Privátní DNS. Musí být také propojena s Virtual Network, která obsahuje soukromý koncový bod. Pokud zóna Privátní DNS není propojená se správným Virtual Network, veškeré rozlišení DNS z tohoto Virtual Network bude Privátní DNS zónu ignorovat.

Otevřete prostředek zóny Privátní DNS a v levé nabídce klikněte na možnost **odkazy virtuální sítě** . Tím se zobrazí seznam odkazů, z nichž každý má název Virtual Network v rámci vašeho předplatného. Tady musí být uveden Virtual Network, který obsahuje prostředek privátního koncového bodu. Pokud tam není, přidejte ho. Podrobný postup najdete v tématu [propojení virtuální sítě](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network). Možnost Povolit automatickou registraci můžete nechat nezaškrtnuté – Tato funkce nesouvisí s privátními odkazy.

Jakmile je zóna Privátní DNS propojená s Virtual Network, budou žádosti DNS pocházející z Virtual Network Hledat názvy v Privátní DNS zóně. To je nutné pro správné vyřešení adres, které se provádí v Virtual Machines připojeném k Virtual Network, kde byl vytvořen privátní koncový bod.

>[!NOTE]
> Pokud jste odkaz právě uložili, může to chvíli trvat, i když na portálu říká dokončení operace. Možná budete muset restartovat virtuální počítač, který používáte k testování překladu názvů DNS.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>Ověřte, že zóna Privátní DNS obsahuje pravý záznam.

Pomocí portálu otevřete zónu Privátní DNS s názvem `privatelink.vaultcore.azure.net` . Na stránce Přehled se zobrazí všechny záznamy. Ve výchozím nastavení bude existovat jeden záznam s názvem `@` a typem `SOA` , což znamená začátek autority. Nemusíte se dotknout.

Aby překlad názvů trezoru klíčů fungoval, musí existovat `A` záznam s jednoduchým názvem trezoru bez přípony nebo teček. Například pokud je název hostitele `fabrikam.vault.azure.net` , musí existovat `A` záznam s názvem `fabrikam` bez přípony nebo teček.

Také hodnota `A` záznamu (IP adresa) musí být [privátní IP adresa trezoru klíčů](#find-the-key-vault-private-ip-address-in-the-virtual-network). Pokud `A` záznam najdete, ale obsahuje je na nesprávnou IP adresu, je nutné odebrat chybnou IP adresu a přidat novou. Doporučuje se odebrat celý `A` záznam a přidat nový.

>[!NOTE]
> Pokaždé, když odeberete nebo upravíte `A` záznam, může se počítač stále překládat na starou IP adresu, protože hodnota TTL (Time to Live) ještě nemusí být vypršet. Doporučuje se vždycky zadat hodnotu TTL, která není menší než 60 sekund (jedna minuta) a nesmí přesáhnout 600 sekund (10 minut). Pokud zadáte příliš velkou hodnotu, vaše obnovení z výpadků může trvat příliš dlouho.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>Překlad názvů DNS pro více než jeden Virtual Network

Pokud existuje více virtuálních sítí a každá z nich má svůj vlastní prostředek privátního koncového bodu odkazující na stejný Trezor klíčů, musí název hostitele trezoru klíčů překládat na jinou privátní IP adresu v závislosti na síti. To znamená, že je potřeba také více Privátní DNS zón, z nichž každá je propojena s jinou Virtual Network a používá jinou IP adresu v `A` záznamu.

V pokročilejších scénářích je více virtuálních sítí s povoleným partnerským vztahem. V takovém případě bude potřebovat pouze jeden Virtual Network prostředek privátního koncového bodu, i když oba mohou být propojeny s prostředkem zóny Privátní DNS. Tento scénář není přímo pokryt tímto dokumentem.

### <a name="fact-you-have-control-over-dns-resolution"></a>Fakt: máte kontrolu nad překladem názvů DNS.

Jak je vysvětleno v [předchozí části](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine), Trezor klíčů s privátními odkazy má alias `{vaultname}.privatelink.vaultcore.azure.net` ve své *veřejné* registraci. Server DNS používaný Virtual Network používá veřejnou registraci, ale kontroluje všechny aliasy pro *soukromou* registraci, a pokud je nalezen, zastaví se následující aliasy definované při veřejné registraci.

Tato logika znamená, že pokud je Virtual Network propojena s Privátní DNSou zónou s názvem `privatelink.vaultcore.azure.net` a veřejná registrace DNS pro Trezor klíčů má alias `fabrikam.privatelink.vaultcore.azure.net` (Všimněte si, že přípona názvu hostitele trezoru klíčů odpovídá přesně názvu zóny privátní DNS), pak bude dotaz DNS hledat `A` záznam s názvem `fabrikam` *v privátní DNS zóně*. Pokud se `A` záznam najde, jeho IP adresa se vrátí v dotazu DNS a při veřejné registraci DNS se neprovádí žádné další vyhledávání.

Jak vidíte, překlad názvů je pod vaším ovládacím prvkem. Pro tento návrh jsou k disdobu tyto důvody:

- Můžete mít složitý scénář, který zahrnuje vlastní servery DNS a integraci s místními sítěmi. V takovém případě je potřeba určit, jak se mají překládat názvy na IP adresy.
- Je možné, že budete potřebovat přístup k trezoru klíčů bez privátních odkazů. V takovém případě musí název hostitele z Virtual Network vracet veřejnou IP adresu a k tomu dojde proto, že trezory klíčů bez privátních odkazů nemají `privatelink` v registraci názvu alias.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. Ověřte, že požadavky na Trezor klíčů používají privátní odkaz.

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>Dotazování `/healthstatus` koncového bodu trezoru klíčů

Váš Trezor klíčů poskytuje `/healthstatus` koncový bod, který se dá použít pro diagnostiku. Hlavičky odpovědi obsahují IP adresu původu, jak je vidět služba trezoru klíčů. Tento koncový bod můžete zavolat pomocí následujícího příkazu (**nezapomeňte použít název hostitele trezoru klíčů**):

Windows (PowerShell):

    PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers

    Key                           Value
    ---                           -----
    Pragma                        no-cache
    x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
    x-ms-keyvault-service-version 1.2.27.0
    x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security     max-age=31536000;includeSubDomains
    Content-Length                4
    Cache-Control                 no-cache
    Content-Type                  application/json; charset=utf-8

Linux nebo nejnovější verze systému Windows 10, které zahrnují `curl` :

    joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
    x-ms-keyvault-service-version: 1.2.27.0
    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security: max-age=31536000;includeSubDomains
    Content-Length: 4

Pokud nezískáváte výstup podobný tomuto, nebo pokud se zobrazí chyba sítě, znamená to, že váš Trezor klíčů není přístupný prostřednictvím zadaného názvu hostitele ( `fabrikam.vault.azure.net` v příkladu). Název hostitele se nepřekladuje na správnou IP adresu nebo máte problém s připojením na transportní vrstvě. Může to být způsobeno problémy se směrováním, zavoláním balíčku a dalšími důvody. Je nutné prozkoumat další.

Odpověď musí zahrnovat hlavičku `x-ms-keyvault-network-info` :

    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;

V `addr` poli v `x-ms-keyvault-network-info` hlavičce se zobrazuje IP adresa původu požadavku. Tato IP adresa může být jedna z následujících:

- Soukromá IP adresa počítače, který provádí požadavek. To znamená, že požadavek používá privátní odkazy nebo koncové body služby. Toto je očekávaný výsledek pro privátní odkazy.
- Některá jiná privátní IP adresa, *nikoli* počítač, který provádí požadavek. To znamená, že některé vlastní směrování jsou efektivní. Stále indikuje, že požadavek používá privátní odkazy nebo koncové body služby.
- Některá veřejná IP adresa. To znamená, že žádost je směrována na Internet prostřednictvím zařízení brány (NAT). To znamená, že požadavek nepoužívá privátní odkazy a je třeba opravit nějaký problém. Mezi běžné příčiny patří 1) privátní koncový bod není ve stavu schváleno a úspěšné; a 2) název hostitele nepřekladuje na privátní IP adresu trezoru klíčů. Tento článek zahrnuje akce řešení potíží v obou případech.

>[!NOTE]
> Pokud požadavek `/healthstatus` funguje, ale `x-ms-keyvault-network-info` chybí hlavička, pak koncový bod nejspíš neobsluhuje Trezor klíčů. Vzhledem k tomu, že výše uvedené příkazy také ověřují certifikát HTTPS, chybějící záhlaví může být znaménkem manipulace.

### <a name="query-the-key-vault-ip-address-directly"></a>Dotazování na IP adresu trezoru klíčů přímo

>[!IMPORTANT]
> Přístup k trezoru klíčů bez ověření certifikátu HTTPS je nebezpečný a dá se použít jenom pro účely učení. Kód v produkčním prostředí nesmí nikdy přistupovat k trezoru klíčů bez tohoto ověření na straně klienta. I když se jenom diagnostikují problémy, může vás podléhat průběžnému pokusu o manipulaci, který se nezobrazí, pokud v žádostech na Trezor klíčů vždycky zakážete ověřování certifikátů pomocí protokolu HTTPS.

Pokud jste nainstalovali nejnovější verze prostředí PowerShell, můžete použít `-SkipCertificateCheck` k přeskočení kontrol certifikátů protokolu HTTPS a pak můžete přímo cílit na [IP adresu trezoru klíčů](#find-the-key-vault-private-ip-address-in-the-virtual-network) :

    PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers

Pokud používáte `curl` , můžete to samé provést s `-k` argumentem:

    joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus

Odpovědi by měly být stejné jako v předchozím oddílu, což znamená, že musí obsahovat `x-ms-keyvault-network-info` hlavičku se stejnou hodnotou. `/healthstatus`Pokud používáte název hostitele trezoru klíčů nebo IP adresu, koncový bod nezáleží.

Pokud se zobrazí `x-ms-keyvault-network-info` vrácení jedné hodnoty pro požadavek pomocí názvu hostitele trezoru klíčů a další hodnota pro požadavek s použitím IP adresy, pak každý požadavek cílí na jiný koncový bod. Chcete-li se `addr` `x-ms-keyvault-network-info` rozhodnout, které případy jsou chybné a je nutné je opravit, přečtěte si vysvětlení pole z předchozí části.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. další změny a přizpůsobení, která způsobují dopad

Následující položky jsou nevyčerpávající akce šetření. Dozvíte se, kde můžete hledat další problémy, ale musíte mít pokročilou znalost sítě, abyste mohli opravit problémy v těchto scénářích.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Diagnostika vlastních serverů DNS na Virtual Network

Na portálu otevřete prostředek Virtual Network. V nabídce vlevo otevřete **servery DNS**. Pokud používáte Custom (vlastní), pak překlad DNS nemusí být popsaný v tomto dokumentu. Musíte diagnostikovat, jak vaše servery DNS řeší název hostitele trezoru klíčů.

Pokud používáte výchozí servery DNS poskytované Azure, je možné použít celý dokument.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>Diagnostika hostitelů přepisování nebo vlastních serverů DNS na virtuálním počítači

Řada operačních systémů povoluje nastavení explicitní pevné IP adresy na název hostitele, obvykle změnou `hosts` souboru. Můžou taky umožňovat přepsání serverů DNS. Pokud používáte některý z těchto scénářů, pokračujte v možnosti diagnostiky specifické pro systém.

### <a name="promiscuous-proxies-fiddler-etc"></a>Promiskuitní proxy (Fiddler atd.)

S výjimkou výslovně uvedených možností diagnostiky v tomto článku fungují pouze v případě, že v prostředí není k dispozici žádný smíšený proxy server. I když jsou tyto proxy servery často nainstalovány výhradně v počítači, který je diagnostikován (Fiddler je nejběžnější příklad), mohou pokročilí správci přepsat kořenové certifikační autority (CAs) a nainstalovat promiskuitní proxy server v zařízeních brány, které obsluhují více počítačů v síti. Tyto proxy servery můžou významně ovlivnit zabezpečení i spolehlivost. Společnost Microsoft nepodporuje konfigurace, které tyto produkty používají.

### <a name="other-things-that-may-affect-connectivity"></a>Další věci, které mohou mít vliv na připojení

Toto je nevyčerpávající seznam položek, které se dají najít v rozšířených nebo složitých scénářích:

- Nastavení brány firewall, buď Azure Firewall připojená k Virtual Network, nebo vlastní řešení brány firewall nasazené v Virtual Network nebo v počítači.
- Partnerský vztah k síti, který může mít vliv na to, které servery DNS se používají a jakým způsobem se směruje provoz.
- Řešení vlastní brány (NAT), která mohou ovlivnit způsob směrování provozu, včetně provozu z dotazů DNS.
