---
title: Integrace Palo Alto
description: Defender for IoT integruje svou nepřetržitou platformu monitorování hrozeb pro sdílení po internetu s bránou firewall pro nové generace v Palo Alto, aby bylo možné blokovat kritické hrozby, rychleji a efektivněji.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 49c00f9ad7cc1980a8690e35ed19351e20417c6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783927"
---
# <a name="about-the-palo-alto-integration"></a>O integraci Palo Alto

Defender for IoT integruje svou nepřetržitou platformu monitorování hrozeb pro sdílení po internetu s bránou firewall pro nové generace v Palo Alto, aby bylo možné blokovat kritické hrozby, rychleji a efektivněji.

K dispozici jsou následující typy integrace:

- Možnost automatického blokování: přímý Defender pro IoT-Palo Alto Integration

- Odesílání doporučení pro blokování do centrálního systému pro správu: Defender pro IoT-Panorama Integration

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>Konfigurace okamžitého blokování podle zadané brány firewall Palo Alto

V kritických případech, jako jsou například výstrahy týkající se malwaru, můžete povolit automatické blokování. To se provádí konfigurací pravidla předávání v programu Defender pro IoT, které odesílá blokující příkaz přímo do konkrétní brány firewall Palo Alto.

Když Defender pro IoT identifikuje kritickou hrozbu, pošle výstrahu, která obsahuje možnost blokování infikovaného zdroje. Výběrem **zdroje bloků** v podrobnostech výstrahy aktivujete pravidlo předávání, které odešle blokující příkaz do zadané brány firewall Palo Alto.

Konfigurace:

1. V levém podokně vyberte **předat dál**.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="Obrazovka s výstrahou pro přeposílání.":::

1. Vyberte **vytvořit pravidlo předávání**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Vytvořit předávací pravidlo":::

1. Konfigurace pravidla pro předávání Palo Alto NGFW:  
 
   - Definujte parametry standardního pravidla a z rozevíracího seznamu **Akce** vyberte **Odeslat do Palo Alto NGFW**.
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="Upravte pravidlo pro předávání.":::

1. V podokně akce nastavte následující parametry:

   - **Hostitel**: zadejte IP adresu serveru NGFW.
   - **Port**: zadejte port serveru NGFW.
   - **Uživatelské jméno**: zadejte uživatelské jméno NGFW serveru.
   - **Heslo**: zadejte heslo serveru NGFW.
   - **Konfigurace**: nastavte následující možnosti, které umožní blokování podezřelých zdrojů pomocí brány firewall Palo Alto:
     - **Blokovat neplatné kódy funkcí**: porušení protokolu – neplatná hodnota pole porušující specifikaci protokolu ICS (potenciální zneužití).
     - **Blokovat neoprávněné programování a aktualizace firmwaru pro PLC**: neautorizované změny PLC
     - **Zablokovat neoprávněný PLC**: Zastavte PLC (výpadek).
     - **Blokuje výstrahy týkající se malwaru**: blokování průmyslových pokusů o malware (Triton, NotPetya atd.). Můžete vybrat možnost **automatického blokování**. V takovém případě se blokování spustí automaticky a okamžitě.
     - **Blokuje neoprávněné prohledávání**: neoprávněné prohledávání (potenciální rekognoskace).
     
1. Vyberte **Odeslat**.

Blokování podezřelého zdroje: 

1. V podokně **výstrahy** vyberte výstrahu související s integrací Palo Alto. Zobrazí se dialogové okno **Podrobnosti výstrahy** .
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Podrobnosti výstrahy":::

1. Chcete-li automaticky blokovat podezřelý zdroj, vyberte možnost **zdroj bloku**. Zobrazí se dialogové okno **Potvrdit** .

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Potvrďte blokování na obrazovce potvrďte prosím.":::

1. V dialogovém okně **Potvrdit** vyberte **OK**. Podezřelý zdroj je blokován bránou firewall Palo Alto.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Odesílání zásad blokování do Palo Alto panoramat

Defender pro sítě IoT a Palo Alto má integraci mimo polici, která automaticky vytváří nové zásady v Palo Alto Networks NMS, panoramat. Tato integrace vyžaduje potvrzení správcem panoramatické a nepovoluje automatické blokování.

Integrace je určena pro následující incidenty:

- **Neautorizované změny pro PLC:** Aktualizace logiky žebříku nebo firmwaru zařízení. To může představovat legitimní aktivitu nebo pokus o ohrožení zařízení. Ohrožení zabezpečení může nastat vložením škodlivého kódu, jako je například trojský kůň vzdáleného přístupu (RAT) nebo parametry, které způsobují nezabezpečený provoz fyzického procesu, jako je například spřádání turbíny.

- **Porušení protokolu:** Struktura paketu nebo hodnota pole, která je v rozporu se specifikací protokolu. To může představovat nesprávně nakonfigurovanou aplikaci nebo škodlivý pokus o ohrožení zařízení. Například způsobuje stav přetečení vyrovnávací paměti v cílovém zařízení.

- **Zastavení PLC:** Příkaz, který způsobí, že zařízení přestane fungovat, což znamená riziko fyzického procesu, který ovládá PLC.

- **Průmyslový malware nalezený v síti ICS:** Malware, který pracuje se zařízeními ICS pomocí jejich nativních protokolů, jako jsou TRITON a Industroyer. Defender pro IoT také detekuje malware v IT, který se později přesunul do prostředí ICS a SCADA, jako je například Conficker, WannaCry a NotPetya.

- **Kontroluje se malware:** Nástroje rekognoskace, které shromažďují data o konfiguraci systému v rámci fáze předběžného útoku. Trojský kůň Havex například prohledává průmyslové sítě pro zařízení pomocí OPC, což je standardní protokol používaný systémy SCADA systémů Windows ke komunikaci se zařízeními ICS.

## <a name="the-process"></a>Proces

Když Defender pro IoT detekuje předem konfigurovaný případ použití, přidá se do výstrahy tlačítko **zdroje bloku** . Když pak uživatel **CyberX** vybere tlačítko pro **blok zdroje** , Defender pro IoT vytvoří zásady na panoramatické odesláním předdefinovaného předávacího pravidla.

Zásada se použije jenom v případě, že ji správce panoramatického doručí do relevantního NGFW v síti.

V sítích IT můžou existovat dynamické IP adresy. Pro tyto podsítě proto musí být zásady založené na plně kvalifikovaném názvu domény (název DNS), nikoli na IP adrese. Defender for IoT provede zpětné vyhledávání a porovná zařízení s dynamickou IP adresou a jejich plně kvalifikovaným názvem domény (název DNS) při každém nakonfigurovaném počtu hodin.

Kromě toho Defender for IoT pošle e-mailem příslušnému uživateli panoramat, aby oznámil, že nové zásady vytvořené v programu Defender pro IoT čekají na schválení. Následující obrázek představuje Defender pro IoT-Panorama integrační architekturu.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="Architektura integrace CyberX-panoramatické":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Vytvoření zásad pro blokování panoramatického přístupu v programu Defender pro konfiguraci IoT

### <a name="to-configure-dns-lookup"></a>Konfigurace vyhledávání DNS

1. V levém podokně vyberte **nastavení systému**.

1. V podokně **nastavení systému** vyberte **nastavení DNS** :::image type="icon" source="media/integration-paloalto/settings.png"::: .

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="Nakonfigurujte nastavení DNS.":::

1. V dialogovém okně **Upravit nastavení DNS** nastavte následující parametry:

   - **Stav**: stav překladače DNS.

   - **Adresa serveru DNS**: zadejte IP adresu nebo plně kvalifikovaný název domény síťového serveru DNS.
   - **Port serveru DNS**: zadejte port, který se používá k dotazování serveru DNS.
   - **Podsítě**: Nastavte rozsah podsítě dynamických IP adres. Rozsah, který program Defender pro IoT vrátí na adresu vyhledávání na serveru DNS, aby odpovídal jejich aktuálnímu názvu FQDN.
   - **Naplánování zpětného vyhledávání**: Definujte možnosti plánování následujícím způsobem:
     - Podle určitých časů: Určete, kdy se má zpětné vyhledávání provést každý den.
     - Podle pevných intervalů (v hodinách): nastavte četnost zpětného vyhledávání.
   - **Počet popisků**: Dejte programu Defender, aby IoT automaticky VYŘEŠIL síťové IP adresy na plně kvalifikované názvy domén zařízení. <br />Pokud chcete nakonfigurovat rozlišení doménového názvu DNS, přidejte počet popisků domény, které se mají zobrazit. Od zleva doprava se zobrazí až 30 znaků.
1. Vyberte **Uložit**.
1. Pokud chcete zajistit správnost nastavení DNS, vyberte **Vyhledat test**. Test zajišťuje správné nastavení IP adresy serveru DNS a portu serveru DNS.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Konfigurace pravidla předávání pro blokování podezřelého provozu pomocí brány firewall Palo Alto

1. V levém podokně vyberte **předat dál**. Zobrazí se podokno předávání dál.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="Obrazovka pro přesměrování.":::

1. V podokně **předávání dál** vyberte **vytvořit pravidlo předávání**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Vytvořit předávací pravidlo":::

1. Konfigurace pravidla předávání Palo Alto panoramatické:

   Definujte parametry standardního pravidla a v rozevíracím seznamu **Akce** vyberte **Odeslat do Palo Alto panoramat**. Zobrazí se podokno Podrobnosti akce.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="Vybrat akci":::

1. V podokně akce nastavte následující parametry:

   - **Hostitel**: zadejte IP adresu panoramatického serveru.

   - **Port**: zadejte port panoramatického serveru.
   - **Uživatelské jméno**: zadejte uživatelské jméno panoramatického serveru.
   - **Heslo**: zadejte heslo panoramatického serveru.
   - **Adresa sestavy**: Určete, jak se má blokování spouštět, následovně:
   
     - **Podle IP adresy**: vždy vytvoří blokující zásady na panoramatu na základě IP adresy.
     
     - **Podle plně kvalifikovaného názvu domény nebo IP adresy**: vytváří blokující zásady na panoramatu založené na plně kvalifikovaném názvu domény (pokud existuje), jinak IP adresou.
     
   - **E-mail**: nastavte e-mailovou adresu pro e-mail s oznámením zásad.
     > [!NOTE]
     > Ujistěte se, že jste v programu Defender pro IoT nakonfigurovali poštovní server. Pokud není zadána žádná e-mailová adresa, Defender pro IoT nepošle e-mailovou zprávu s oznámením.
   - **Spustit vyhledávání DNS při detekci výstrahy (zaškrtávací políčko)**: Pokud je v adrese sestavy nastavená možnost podle plně kvalifikovaného názvu domény nebo IP adresy. Toto zaškrtávací políčko je vybráno ve výchozím nastavení. Pokud je nastavená jenom IP adresa, tato možnost je zakázaná.
   - **Konfigurace**: nastavte následující možnosti, které umožní blokování podezřelých zdrojů pomocí Palo Alto panoramat:
   
     - **Blokovat neplatné kódy funkcí**: porušení protokolu – neplatná hodnota pole porušující ICS, specifikaci protokolu (potenciální zneužití).
     
     - **Blokovat neoprávněné programování a aktualizace firmwaru pro PLC**: neautorizované změny PLC
     
     - **Zablokovat neoprávněný PLC**: Zastavte PLC (výpadek).
     
     - **Blokuje výstrahy týkající se malwaru**: blokování průmyslových pokusů o malware (Triton, NotPetya atd.). Můžete vybrat možnost **automatického blokování**. V takovém případě se blokování spustí automaticky a okamžitě.
     
     - **Blokuje neoprávněné prohledávání**: neoprávněné prohledávání (potenciální rekognoskace).
     
1. Vyberte **Odeslat**.

### <a name="to-block-the-suspicious-source"></a>Blokování podezřelého zdroje

1. V podokně **výstrahy** vyberte výstrahu související s integrací Palo Alto. Zobrazí se dialogové okno **Podrobnosti výstrahy** .

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Podrobnosti výstrahy":::        

1. Chcete-li automaticky blokovat podezřelý zdroj, vyberte možnost **zdroj bloku**.

1. V dialogovém okně **Potvrdit** vyberte **OK.**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirm":::

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [přeslat informace o výstrahách](how-to-forward-alert-information-to-partners.md).
