---
title: Role certifikátů X.509 ve službě Azure IoT Hub Device Provisioning Service
description: Jak srolovat certifikáty X.509 s instancí Služby DPS (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 4d5ddb229cd6a41235990437bc0f8db08e3381ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974883"
---
# <a name="how-to-roll-x509-device-certificates"></a>Jak roll X.509 certifikáty zařízení

Během životního cyklu vašeho řešení IoT budete muset rozbalit certifikáty. Dva z hlavních důvodů pro postupné certifikáty by porušení zabezpečení a vypršení platnosti certifikátu. 

Postupné certifikáty je osvědčený postup zabezpečení, který pomáhá zabezpečit systém v případě porušení zabezpečení. Jako součást [assume breach methodology](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), Microsoft obhajuje potřebu mít reaktivní bezpečnostní procesy na místě spolu s preventivními opatřeními. Aktualizace certifikátů zařízení by měla být součástí těchto procesů zabezpečení. Četnost, ve které vaše certifikáty budou vysuzovány, bude záviset na potřebách zabezpečení vašeho řešení. Zákazníci s řešeními zahrnujícími vysoce citlivá data mohou každý den rozbalovat certifikáty, zatímco jiní své certifikáty každých pár let svádějí.

Zvlněné certifikáty zařízení bude zahrnovat aktualizaci certifikátu uloženého na zařízení a služby IoT hub. Poté se zařízení může znovu zřídit pomocí služby IoT hub pomocí [normálního automatického zřizování](concepts-auto-provisioning.md) pomocí služby Zřizování zařízení.


## <a name="obtain-new-certificates"></a>Získat nové certifikáty

Existuje mnoho způsobů, jak získat nové certifikáty pro vaše zařízení IoT. Patří mezi ně získání certifikátů z továrny zařízení, generování vlastních certifikátů a správa vytváření certifikátů třetí stranou. 

Certifikáty jsou navzájem podepsány a vytvářejí řetězec důvěryhodnosti od kořenového certifikátu certifikační autority k [listu](concepts-security.md#end-entity-leaf-certificate). Podpisový certifikát je certifikát používaný k podepsání listu certifikátu na konci řetězce důvěryhodnosti. Podpisový certifikát může být kořenový certifikát certifikační autority nebo zprostředkující certifikát v řetězci důvěryhodnosti. Další informace naleznete v [tématu Certifikáty X.509](concepts-security.md#x509-certificates).
 
Podpisový certifikát lze získat dvěma různými způsoby. Prvním způsobem, který je doporučen pro produkční systémy, je zakoupení podpisového certifikátu od kořenové certifikační autority .) Tímto způsobem řetězy zabezpečení až na důvěryhodný zdroj. 

Druhým způsobem je vytvořit si vlastní certifikáty X.509 pomocí nástroje, jako je OpenSSL. Tento přístup je skvělý pro testování certifikátů X.509, ale poskytuje několik záruk týkajících se zabezpečení. Tento přístup doporučujeme používat pouze k testování, pokud jste připraveni jednat jako vlastní poskytovatel certifikační autority.
 

## <a name="roll-the-certificate-on-the-device"></a>Nahodit certifikát na zařízení

Certifikáty na zařízení by měly být vždy uloženy na bezpečném místě, jako je [modul hardwarového zabezpečení (HSM).](concepts-device.md#hardware-security-module) Způsob, jakým role certifikáty zařízení bude záviset na tom, jak byly vytvořeny a nainstalovány v zařízeních na prvním místě. 

Pokud jste certifikáty získali od třetí strany, musíte se podívat na to, jak své certifikáty převádějí. Tento proces může být zahrnut do vaší dohody s nimi, nebo to může být samostatná služba, kterou nabízejí. 

Pokud spravujete vlastní certifikáty zařízení, budete muset vytvořit vlastní kanál pro aktualizaci certifikátů. Ujistěte se, že staré i nové listové certifikáty mají stejný běžný název (CN). Tím, že má stejnou kn, zařízení může reprovision sám bez vytvoření duplicitní registrační záznam. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Zasuňte certifikát do centra IoT hub

Certifikát zařízení lze ručně přidat do služby IoT hub. Certifikát lze také automatizovat pomocí instance služby Device Provisioning. V tomto článku budeme předpokládat, že instance služby zřizování zařízení se používá k podpoře automatického zřizování.

Když zařízení je zpočátku zřídit prostřednictvím automatického zřizování, jeho spuštění a kontakty zřizovací služby. Služba zřizování reaguje provedením kontroly identity před vytvořením identity zařízení v centru IoT pomocí listu zařízení certifikát jako pověření. Služba zřizování pak sdělí zařízení, ke kterému je službě IoT hub přiřazeno, a zařízení pak použije svůj listový certifikát k ověření a připojení k centru IoT. 

Jakmile je nový listový certifikát vrácen do zařízení, nemůže se již připojit k centru IoT, protože k připojení používá nový certifikát. Centrum IoT rozpoznává pouze zařízení se starým certifikátem. Výsledkem pokusu o připojení zařízení bude chyba "neoprávněného" připojení. Chcete-li tuto chybu vyřešit, je nutné aktualizovat položku zápisu pro zařízení, aby účet pro nové listový certifikát zařízení. Pak zřizovací služba můžete aktualizovat informace o registru zařízení služby IoT Hub podle potřeby, když je zařízení znovu zřízena. 

Jednou z možných výjimek z této selhání připojení by byl scénář, ve kterém jste vytvořili [skupinu registrace](concepts-service.md#enrollment-group) pro vaše zařízení ve službě zřizování. V tomto případě pokud nejste postupné kořenové nebo zprostředkující certifikáty v řetězu certifikátů zařízení důvěryhodnosti, pak zařízení bude rozpoznán, pokud nový certifikát je součástí řetězce důvěryhodnosti definované ve skupině zápisu. Pokud tento scénář vzniká jako reakce na narušení zabezpečení, měli byste alespoň na černou listinu certifikáty konkrétní zařízení ve skupině, které jsou považovány za porušení. Další informace naleznete [v tématu Blacklist konkrétní zařízení ve skupině registrace](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

Aktualizace položek zápisu pro vrácené certifikáty se provádí na stránce **Spravovat zápisy.** Chcete-li získat přístup k této stránce, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte na instanci služby Zřizování zařízení služby IoT Hub, která má položku registrace pro vaše zařízení.

2. Klikněte na **Správa registrací**.

    ![Správa registrací](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Způsob zpracování aktualizace položky registrace bude záviset na tom, zda používáte jednotlivé registrace nebo registraci skupin. Doporučené postupy se také liší v závislosti na tom, zda jste postupné certifikáty z důvodu porušení zabezpečení nebo vypršení platnosti certifikátu. Následující části popisují, jak zpracovat tyto aktualizace.


## <a name="individual-enrollments-and-security-breaches"></a>Individuální registrace a narušení zabezpečení

Pokud jste postupné certifikáty v reakci na porušení zabezpečení, měli byste použít následující přístup, který odstraní aktuální certifikát okamžitě:

1. Klikněte na **Jednotlivé registrace**a klikněte na položku ID registrace v seznamu. 

2. Klepněte na tlačítko **Odstranit aktuální certifikát** a potom klepnutím na ikonu složky vyberte nový certifikát, který chcete odeslat pro položku zápisu. Po dokončení klikněte na **Uložit.**

    Tyto kroky by měly být dokončeny pro primární a sekundární certifikát, pokud jsou oba ohroženy.

    ![Správa jednotlivých registrací](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Po odebrání ohroženého certifikátu ze služby zřizování lze certifikát stále použít k připojení zařízení k centru IoT, pokud tam existuje registrace zařízení. Můžete řešit to to to toto dvěma způsoby: 

    Prvním způsobem by bylo ručně přejít do služby IoT hub a okamžitě odebrat registraci zařízení přidružené k ohroženému certifikátu. Poté, když zařízení znovu zřídí aktualizovaný certifikát, vytvoří se nová registrace zařízení.     

    ![Odebrání registrace zařízení centra IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druhým způsobem by bylo použít podporu opětovného zřízení k opětovnému zřízení zařízení do stejného centra IoT hub. Tento přístup lze nahradit certifikát pro registraci zařízení v centru IoT. Další informace naleznete v tématu [Jak znovu zřídit zařízení](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Individuální zápisy a vypršení platnosti certifikátů

Pokud jste postupné certifikáty pro zpracování vypršení platnosti certifikátů, měli byste použít konfiguraci sekundárního certifikátu takto snížit prostoje pro zařízení, která se pokoušejí zřídit.

Později, když sekundární certifikát také blíží vypršení platnosti a je třeba je vrátit, můžete otočit na pomocí primární konfigurace. Otáčení mezi primárními a sekundárními certifikáty tímto způsobem snižuje prostoje zařízení, která se pokoušejí o zřízení.


1. Klikněte na **Jednotlivé registrace**a klikněte na položku ID registrace v seznamu. 

2. Klikněte na **Sekundární certifikát** a potom klikněte na ikonu složky a vyberte nový certifikát, který chcete nahrát pro položku zápisu. Klikněte na **Uložit**.

    ![Správa jednotlivých zápisů pomocí sekundárního certifikátu](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Později po vypršení platnosti primárního certifikátu se vraťte a odstraňte tento primární certifikát klepnutím na tlačítko **Odstranit aktuální certifikát.**

## <a name="enrollment-groups-and-security-breaches"></a>Skupiny zápisů a narušení zabezpečení

Chcete-li aktualizovat registraci skupiny v reakci na porušení zabezpečení, měli byste použít jeden z následujících přístupů, které okamžitě odstraní aktuální kořenovou certifikační autoritu nebo zprostředkující certifikát.

#### <a name="update-compromised-root-ca-certificates"></a>Aktualizace kompromitovaných kořenových certifikátů certifikační autority

1. Klikněte na kartu Certifikáty pro instanci **služby** Zřizování zařízení.

2. Klikněte na kompromitovaný certifikát v seznamu a potom klikněte na tlačítko **Odstranit.** Potvrďte odstranění zadáním názvu certifikátu a klepnutím na tlačítko **OK**. Tento postup opakujte pro všechny ohrožené certifikáty.

    ![Odstranit kořenový certifikát certifikační autority](./media/how-to-roll-certificates/delete-root-cert.png)

3. Postupujte podle pokynů uvedených v [části Konfigurace ověřených certifikátů certifikační autority](how-to-verify-certificates.md) pro přidání a ověření nových kořenových certifikátů certifikační autority.

4. Klikněte na kartu Spravovat registrace pro instanci **služby** Zřizování zařízení a klikněte na seznam **Skupiny registrace.** Klikněte v seznamu na název skupiny pro zápis.

5. Klikněte na **certifikát certifikační autority**a vyberte nový kořenový certifikát certifikační autority. Potom klepněte na tlačítko **Uložit**. 

    ![Výběr nového kořenového certifikátu certifikační autority](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Po odebrání ohroženého certifikátu ze služby zřizování lze certifikát stále použít k připojení zařízení k centru IoT, pokud tam existují registrace zařízení. Můžete řešit to to to toto dvěma způsoby: 

    Prvním způsobem by bylo ručně přejít do služby IoT hub a okamžitě odebrat registraci zařízení přidružené k ohroženému certifikátu. Poté, když vaše zařízení znovu zřídí aktualizované certifikáty, vytvoří se pro každý z nich nová registrace zařízení.     

    ![Odebrání registrace zařízení centra IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druhým způsobem by bylo použít podporu reprovisioning znovu zřídit zařízení do stejného centra IoT hub. Tento přístup lze nahradit certifikáty pro registrace zařízení v centru IoT. Další informace naleznete v tématu [Jak znovu zřídit zařízení](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Aktualizace kompromitovaných zprostředkujících certifikátů

1. Klikněte na **Skupiny registrace**a potom klikněte na název skupiny v seznamu. 

2. Klepněte na **položku Zprostředkující certifikát**a **odstranit aktuální certifikát**. Kliknutím na ikonu složky přejdete na nový zprostředkující certifikát, který chcete nahrát pro skupinu zápisů. Až budete hotovi, klikněte na **Uložit.** Tyto kroky by měly být dokončeny pro primární i sekundární certifikát, pokud jsou oba ohroženy.

    Tento nový zprostředkující certifikát by měl být podepsán ověřeným kořenovým certifikátem certifikační autority, který již byl přidán do zřizovací služby. Další informace naleznete v [tématu Certifikáty X.509](concepts-security.md#x509-certificates).

    ![Správa jednotlivých registrací](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Po odebrání ohroženého certifikátu ze služby zřizování lze certifikát stále použít k připojení zařízení k centru IoT, pokud tam existují registrace zařízení. Můžete řešit to to to toto dvěma způsoby: 

    Prvním způsobem by bylo ručně přejít do služby IoT hub a okamžitě odebrat registraci zařízení přidružené k ohroženému certifikátu. Poté, když vaše zařízení znovu zřídí aktualizované certifikáty, vytvoří se pro každý z nich nová registrace zařízení.     

    ![Odebrání registrace zařízení centra IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druhým způsobem by bylo použít podporu reprovisioning znovu zřídit zařízení do stejného centra IoT hub. Tento přístup lze nahradit certifikáty pro registrace zařízení v centru IoT. Další informace naleznete v tématu [Jak znovu zřídit zařízení](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Skupiny zápisu a vypršení platnosti certifikátu

Pokud jste postupné certifikáty pro zpracování vypršení platnosti certifikátů, měli byste použít konfiguraci sekundárního certifikátu takto, abyste zajistili žádné prostoje pro zařízení, která se pokoušejí zřídit.

Později, když sekundární certifikát také blíží vypršení platnosti a je třeba je vrátit, můžete otočit na pomocí primární konfigurace. Otáčenímezi primárními a sekundárními certifikáty tímto způsobem zajišťuje žádné prostoje pro zařízení, která se pokoušejí o zřízení. 

#### <a name="update-expiring-root-ca-certificates"></a>Aktualizace kořenových certifikátů certifikační autority s končící platností

1. Postupujte podle pokynů uvedených v [části Konfigurace ověřených certifikátů certifikační autority](how-to-verify-certificates.md) pro přidání a ověření nových kořenových certifikátů certifikační autority.

2. Klikněte na kartu Spravovat registrace pro instanci **služby** Zřizování zařízení a klikněte na seznam **Skupiny registrace.** Klikněte v seznamu na název skupiny pro zápis.

3. Klepněte na **položku Certifikát certifikační autority**a vyberte nový kořenový certifikát certifikační autority v konfiguraci **sekundárního certifikátu.** Potom klepněte na tlačítko **Uložit**. 

    ![Výběr nového kořenového certifikátu certifikační autority](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Později po vypršení platnosti primárního certifikátu klikněte na kartu Certifikáty pro instanci **služby** Zřizování zařízení. Klikněte na certifikát, jehož platnost vypršela v seznamu, a potom klikněte na tlačítko **Odstranit.** Potvrďte odstranění zadáním názvu certifikátu a klepněte na tlačítko **OK**.

    ![Odstranit kořenový certifikát certifikační autority](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Aktualizace zprostředkujících certifikátů s končící platností


1. Klikněte na **Skupiny registrace**a klikněte na název skupiny v seznamu. 

2. Klikněte na **Sekundární certifikát** a potom klikněte na ikonu složky a vyberte nový certifikát, který chcete nahrát pro položku zápisu. Klikněte na **Uložit**.

    Tento nový zprostředkující certifikát by měl být podepsán ověřeným kořenovým certifikátem certifikační autority, který již byl přidán do zřizovací služby. Další informace naleznete v [tématu Certifikáty X.509](concepts-security.md#x509-certificates).

   ![Správa jednotlivých zápisů pomocí sekundárního certifikátu](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Později po vypršení platnosti primárního certifikátu se vraťte a odstraňte tento primární certifikát klepnutím na tlačítko **Odstranit aktuální certifikát.**


## <a name="reprovision-the-device"></a>Opětovné zřízení zařízení

Jakmile je certifikát vrácen na zařízení i zřizování zařízení služby, zařízení můžete znovu zřídit sám kontaktováním služby Device Provisioning. 

Jeden snadný způsob, jak programovací zařízení reprovision je naprogramovat zařízení kontaktovat zřizovací služby projít tok zřizování, pokud zařízení obdrží "neoprávněné" chyba z pokusu o připojení k centru IoT.

Dalším způsobem je pro staré i nové certifikáty, které mají být platné pro krátké překrytí a pomocí služby IoT hub odeslat příkaz do zařízení, aby je znovu zaregistrovat prostřednictvím zřizovací služby aktualizovat jejich informace o připojení služby IoT Hub. Vzhledem k tomu, že každé zařízení může zpracovávat příkazy jinak, budete muset naprogramovat zařízení, abyste věděli, co dělat, když je příkaz vyvolán. Existuje několik způsobů, jak můžete příkaz zařízení prostřednictvím služby IoT Hub a doporučujeme použít [přímé metody](../iot-hub/iot-hub-devguide-direct-methods.md) nebo [úlohy](../iot-hub/iot-hub-devguide-jobs.md) k zahájení procesu.

Po dokončení opětovného zřízení se zařízení budou moct připojit k IoT Hubu pomocí jejich nových certifikátů.


## <a name="blacklist-certificates"></a>Blacklist certifikáty

V reakci na narušení zabezpečení může být nutné na černou listinu certifikát zařízení. Chcete-li na černou listinu certifikát zařízení, zakažte položku zápisu pro cílové zařízení nebo certifikát. Další informace najdete v článku Správa [registrace](how-to-revoke-device-access-portal.md) zařízení na černé listině.

Jakmile je certifikát součástí zakázané položky zápisu, všechny pokusy o registraci v centru IoT pomocí tohoto certifikátu se nezdaří, i když je povolen jako součást jiné položky zápisu.
 



## <a name="next-steps"></a>Další kroky

- Další informace o certifikátech X.509 ve službě Device Provisioning Service najdete v [tématu Zabezpečení](concepts-security.md) 
- Informace o tom, jak provést ověření vlastnictví certifikátů X.509 certifikační autority pomocí služby Azure IoT Hub Device Provisioning Service, najdete v tématu [Jak ověřit certifikáty](how-to-verify-certificates.md)
- Informace o tom, jak pomocí portálu vytvořit skupinu registrací, najdete v [tématu Správa registrací zařízení pomocí portálu Azure .](how-to-manage-enrollments.md)










