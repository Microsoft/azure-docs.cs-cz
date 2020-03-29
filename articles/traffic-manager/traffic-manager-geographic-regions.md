---
title: Hierarchie země nebo oblasti používaná zeměpisným směrováním – Azure Traffic Manager
description: Tento článek uvádí hierarchii zemí nebo oblastí používanou typem geografického směrování Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: rohink
ms.openlocfilehash: 19445c06ccf08d2d7916545ad495c56883616c7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938647"
---
# <a name="countryregion-hierarchy-used-by-azure-traffic-manager-for-geographic-traffic-routing-method"></a>Hierarchie země nebo oblasti používaná službou Azure Traffic Manager pro metodu směrování geografického provozu

Tento článek uvádí země a oblasti používané metodou **směrování geografického** provozu ve Správci provozu Azure. Tyto informace můžete také získat programově voláním [rozhraní REST API správce provozu Azure](https://docs.microsoft.com/rest/api/trafficmanager/). 

- SVĚT (svět)

    - GEO-EU (Evropa)

        - AD(Andorra)

        - AL (Albánie)

        - AT(Rakousko)

        - AX(Ålandské ostrovy)

        - BA (Bosna a Hercegovina)

        - BE(Belgie)

        - BG(Bulharsko)

        - BY(Bělorusko)

        - CH(Švýcarsko)

        - CY(Kypr)

        - CZ(Česká republika)

        - DE(Německo)

        - DK(Dánsko)

        - EE (Estonsko)

        - ES(Španělsko)

        - FI(Finsko)

        - FO (Faerské ostrovy)

        - FR(Francie)

        - GB(Spojené království)

        - GG (Guernsey)

        - GI (Gibraltar)

        - GR(Řecko)

        - HR(Chorvatsko)

        - HU(Maďarsko)

        - IE(Irsko)

        - IM (Ostrov Man)

        - IS (Island)

        - IT (Itálie)

        - JE (Jersey)

        - LI(Lichtenštejnsko)

        - LT (Litva)

        - LU(Lucembursko)

        - LV(Lotyšsko)

        - MC(Monako)

        - MD(Moldavsko)

        - ME(Černá Hora)

        - MK(Severní Makedonie)

        - MT(Malta)

        - NL(Nizozemsko)

        - NE(Norsko)

        - PL(Polsko)

        - PT(Portugalsko)

        - RO(Rumunsko)

        - RS (Srbsko)

        - RU(Rusko)

        - SE(Švédsko)

        - SI(Slovinsko)

        - SJ(Špicberky)

        - SK(Slovensko)

        - SM(San Marino)

        - UA (Ukrajina)
            - Region Krym

        - VA (Vatikán)

        - XJ(Jan Mayen)

        - XK (Kosovo)

    - GEO-ME (Blízký východ)

        - AE(Spojené arabské emiráty)

        - BH(Bahrajn)

        - IL(Izrael)

        - IQ (Irák)

        - IR (Írán)

        - JO(Jordánsko)

        - KW(Kuvajt)

        - LB(Libanon)

        - OM(Omán)

        - PS (palestinská samospráva)

        - QA(Katar)

        - SY (Sýrie)

        - SA(Saúdská Arábie)

        - TR(Turecko)

        - YE(Jemen)

    - GEO-NA(Severní Amerika / Střední Amerika / Karibik)

        - AG(Antigua a Barbuda)

        - AI (Anguilla)

        - AW(Aruba)

        - BB(Barbados)

        - BL (Svatý Bartoména)

        - BM(Bermudy)

        - BQ (Bonaire)

        - BS(Bahamy)

        - BZ(Belize)

        - CA (Kanada)

            - CA-AB(Alberta)

            - CA-BC (Britská Kolumbie)

            - CA-MB(Manitoba)

            - CA-NB (Nový Brunšvik)

            - CA-NL(Newfoundland a Labrador)

            - CA-NS (Nové Skotsko)

            - CA-NT(Severozápadní teritoria)

            - CA-NU(Nunavut)

            - CA-ON (Ontario)

            - CA-PE (ostrov Prince Edwarda)

            - CA-QC(Québec)

            - CA-SK(Saskatchewan)

            - CA-YT(Yukon území)

        - CR (Kostarika)

        - CU(Kuba)

        - CW (CuraÃ § ao)

        - DM(Dominika)

        - DO (Dominikánská republika)

        - GD(Grenada)

        - GL (Grónsko)

        - GP (Guadeloupe)

        - GT (Guatemala)

        - HN(Honduras)

        - HT(Haiti)

        - JM(Jamajka)

        - KN (Svatý Kryštand a Nevis)

        - KY (Kajmanské ostrovy)

        - LC (Svatá Lucie)

        - MF(Svatý Martin)

        - MQ(Martinik)

        - MS(Montserrat)

        - MX (Mexiko)

        - NI (Nikaragua)

        - PA (Panama)

        - PM (Saint Pierre a Miquelon)

        - PR (Portoriko)

        - SV(Salvador)

        - SX(Sint Maarten)

        - TC (Ostrovy Turks a Caicos)

        - TT (Trinidad a Tobago)

        - UM(Americké odlehlé ostrovy)

        - USA (Spojené státy)

            - US-AK(Aljaška)

            - US-AL (Alabama)

            - US-AR (Arkansas)

            - US-AZ (Arizona)

            - US-CA(Kalifornie)

            - US-CO (Colorado)

            - US-CT(Connecticut)

            - US-DC (District of Columbia)

            - US-DE(Delaware)

            - US-FL (Florida)

            - US-GA(Gruzie)

            - US-HI (Havaj)

            - US-IA (Iowa)

            - US-ID(Idaho)

            - US-IL (Illinois)

            - US-IN(Indiana)

            - US-KS (Kansas)

            - US-KY (Kentucky)

            - US-LA (Louisiana)

            - US-MA (Massachusetts)

            - US-MD(Maryland)

            - US-ME (Maine)

            - US-MI (Michigan)

            - US-MN (Minnesota)

            - US-MO(Missouri)

            - US-MS (Mississippi)

            - US-MT (Montana)

            - US-NC (Severní Karolína)

            - US-ND (Severní Dakota)

            - US-NE(Nebraska)

            - US-NH (New Hampshire)

            - US-NJ (New Jersey)

            - US-NM (Nové Mexiko)

            - US-NV(Nevada)

            - US-NY(New York)

            - US-OH (Ohio)

            - US-OK (Oklahoma)

            - US-OR(Oregon)

            - US-PA (Pensylvánie)

            - US-RI(Rhode Island)

            - US-SC (Jižní Karolína)

            - US-SD (Jižní Dakota)

            - US-TN (Tennessee)

            - US-TX (Texas)

            - US-UT(Utah)

            - US-VA (Virginie)

            - US-VT(Vermont)

            - US-WA (Washington)

            - US-WI(Wisconsin)

            - US-WV (Západní Virginie)

            - US-WY (Wyoming)

        - VC (Svatý Vincenc a Grenadiny)

        - VG(Britské Panenské ostrovy)

        - VI (Americké Panenské ostrovy)

        - XE(Sint Eustatius)

        - XS(Saba)

    - GEO-AS (Asie)

        - AF (Afghánistán)

        - AM(Arménie)

        - AZ (Ázerbájdžán)

        - BD(Bangladéš)

        - BN (Brunej)

        - BT (Bhútán)

        - CC(Kokosové (Keeling) ostrovy)

        - CN(Čína)

        - CX (Vánoční ostrov)

        - GE(Gruzie)

        - HK (Hongkong – zvláštní správní oblast)

        - ID(Indonésie)

        - IN(Indie)

        - IO (Britské území v Indickém oceánu)

        - JP(Japonsko)

        - KG(Kyrgyzstán)

        - KH(Kambodža)

        - KP(Severní Korea)

        - KR (Korea)

        - KZ(Kazachstán)

        - LA (Laos)

        - LK(Srí Lanka)

        - MM(Myanmar)

        - MN(Mongolsko)

        - MO (Macao SAR)

        - MV (Maledivy)

        - MY (Malajsie)

        - NP (Nepál)

        - PH(Filipíny)

        - PK(Pákistán)

        - SG (Singapur)

        - TH(Thajsko)

        - TJ (Tádžikistán)

        - TL (Timor_Leste)

        - TM (Turkmenistán)

        - TW(Tchaj-wan)

        - UZ(Uzbekistán)

        - VN(Vietnam)

    - GEO-AF(Afrika)

        - AO(Angola)

        - BF(Burkina Faso)

        - BI(Burundi)

        - BJ(Benin)

        - BV(Bouvetův ostrov)

        - BW(Botswana)

        - CD(Kongo (DRK))

        - CF(Středoafrická republika)

        - CI(Pobřeží slonoviny)

        - CM (Kamerun)

        - CV(Cabo Verde)

        - DJ (Džibutsko)

        - DZ(Alžírsko)

        - EG (Egypt)

        - ER(Eritrea)

        - ET (Etiopie)

        - GA (Gabon)

        - GH (Ghana)

        - GM (Gambie)

        - GN(Guinea)

        - GQ (Rovníková Guinea)

        - GW (Guinea_Bissau)

        - KE (Keňa)

        - KM (Komory)

        - LR(Libérie)

        - LS(Lesotho)

        - LY (Libye)

        - MA(Maroko)

        - MG (Madagaskar)

        - ML(Mali)

        - MR(Mauritánie)

        - MU (Mauricius)

        - MW (Malawi)

        - MZ(Mosambik)

        - NA(Namibie)

        - NE(Niger)

        - NG (Nigérie)

        - RE(Réunion)

        - RW(Rwanda)

        - SC(Seychely)

        - SD(Súdán)

        - SH (St Helena, Nanebevstoupení, Tristan da Cunha)

        - SL(Sierra Leone)

        - SN(Senegal)

        - SO(Somálsko)

        - SS(Jižní Súdán)

        - ST(Svatý Tomáš a Princův ostrov)

        - SZ(Svazijsko)

        - TD (Čad)

        - TF(Francouzská jižní území)

        - TG(Togo)

        - TN(Tunisko)

        - TZ(Tanzanie)

        - UG(Uganda)

        - YT (Mayotte)

        - ZA(Jihoafrická republika)

        - ZM(Zambie)

        - ZW(Zimbabwe)

    - GEO-AN (Antarktida)

        - AQ(Antarktida)

    - GEO-SA (Jižní Amerika)

        - AR(Argentina)

        - BO (Bolívie)

        - BR(Brazílie)

        - CL(Chile)

        - CO (Kolumbie)

        - EC (Ekvádor)

        - FK(Falklandské ostrovy)

        - GF(Francouzská Guyana)

        - GS (Jižní Georgie a Jižní Sandwichovy ostrovy)

        - GY (Guyana)

        - PE(Peru)

        - PY(Paraguay)

        - SR(Surinam)

        - UY (Uruguay)

        - VE(Venezuela)

    - GEO-AP(Austrálie / Pacifik)

        - AS (Americká Samoa)

        - AU (Austrálie)

            - AU-ACT(Australské hlavní teritorium)

            - AU-NSW (Nový Jižní Wales)

            - AU-NT(Severní teritorium)

            - AU-QLD (Queensland)

            - Au-SA (Jižní Austrálie)

            - AU-TAS(Tasmánie)

            - AU-VIC (Viktorie)

            - AU-WA(Západní Austrálie)

        - CK(Cookovy ostrovy)

        - FJ(Fidži)

        - FM (mikronésie)

        - GU(Guam)

        - HM (Heardův ostrov a McDonaldovy ostrovy)

        - KI(Kiribati)

        - MH(Marshallovy ostrovy)

        - MP (Severní Mariany)

        - NC (Nová Kaledonie)

        - NF(Norfolk Island)

        - NR(Nauru)

        - NU(Niue)

        - NZ(Nový Zéland)

        - PF(Francouzská Polynésie)

        - PG(Papua-Nová Guinea)

        - PN(Pitcairnovy ostrovy)

        - PW (Palau)

        - SB(Šalamounovy ostrovy)

        - TK(Tokelau)

        - TO (Tonga)

        - TV (Tuvalu)

        - VU (Vanuatu)

        - WF (Wallis a Futuna)

        - WS(Samoa)

## <a name="next-steps"></a>Další kroky

- Další informace o [metodě směrování geografického provozu najdete ve Službě Azure Traffic Manager](traffic-manager-routing-methods.md#geographic).
