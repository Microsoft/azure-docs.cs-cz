Některé Bing odpovědí zahrnout adres URL na miniatury obsloužených Bing. Můžete změnit velikost a oříznout miniatury. 

> [!NOTE]
> Zajistěte, aby velikost a oříznutí miniatury nabízí scénáři vyhledávání a respektují práva třetích stran, podle potřeby pomocí rozhraní API pro Bing hledání a zobrazit požadavky.


Ke změně velikosti obrázku, zahrnují w (šířka) a (výška) parametrů v adrese URL na miniaturu dotazu. Zadejte šířku a výšku v pixelech. Příklad:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Pokud změníte velikost obrázku, se zachová poměr stran. Pokud chcete zachovat poměr, bílé odsazení mohou být přidány do ohraničení obrázku. Například pokud změníte velikost bitové kopie 480 x 359 na 200 x 200 bez oříznutí, celou šířku obsahuje bitovou kopii ale výška obsahuje 25 pixelů white odsazení v horní a dolní části bitovou kopii. Stejné by mít hodnotu true, pokud image byla 359 x 480 s výjimkou doleva a pravého ohraničení by obsahovat bílé odsazení. Pokud oříznete bitovou kopii, přidat není bílé odsazení.  

 
Následující obrázek znázorňuje původní velikost miniatur bitové kopie (480 x 300).  
  
![Původní bitové kopie na šířku](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Následující obrázek znázorňuje obrázek změně velikosti 200 x 200. Je zachován poměr stran a horní a dolní ohraničení se vyplní s bílou (černý okraj je součástí zobrazíte odsazení).  
  
![Změněnou šířku obrázku](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Pokud zadáte dimenzí, které jsou větší než původní šířky a výšky na bitovou kopii, bitovou kopii doplněno bílé na levého a horního ohraničení.  
  
Oříznutí obrázku, zahrňte c (ořezové) parametr dotazu. Toto jsou možné hodnoty, které můžete určit.  
  
- 4&mdash;nevidomí poměr  
- 7&mdash;inteligentní poměr  
  
Pokud si vyžádáte oříznutí inteligentní poměr (c = 7), bitovou kopii je oříznutý z centra oblasti obrázku, v pasivním při zachování poměru stran obrázku. Oblast zájmu je oblast bitové kopie Bingu určuje obsahuje většinu části import. Následující obrázek znázorňuje oblast příkladu, které vás zajímají.  
  
![Oblast zájmu](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Změní velikost obrázku a požadavku oříznutí inteligentní poměr, bitovou kopii sníží na požadovanou velikost při zachování poměru stran. Obrázek je pak oříznout podle změněnou dimenzí. Například pokud je šířka změněnou velikostí menší než nebo rovna hodnotě výška, bitovou kopii je oříznutý vlevo a vpravo od středu oblast zájmu. Bitovou kopii, jinak hodnota je oříznutý pro horní a dolní Center oblasti zájmu.  
  
 
Následující obrázek znázorňuje obrázek snížen na 200 x 200 pomocí inteligentní poměr oříznutí.  
  
![Na šířku obrázku oříznout na 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Následující obrázek znázorňuje obrázek snížen na 200 × 100 pomocí inteligentní poměr oříznutí.  
   
![Na šířku obrázku muset oříznout, aby 200 × 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Následující obrázek znázorňuje obrázek snížen na 100 x 200 pomocí inteligentní poměr oříznutí.  
  
![Na šířku obrázku oříznout na 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Pokud Bing nemůže určit oblasti obrázku, v, Bing používá skrytá poměr oříznutí.  
  
Pokud si vyžádáte oříznutí skrytá poměr (c = 4), Bing používá následující pravidla oříznout bitovou kopii.  
  
- Pokud (původní šířku obrázku nebo původní výška obrázku) < (požadovanou šířku obrázku / požadovaná výška obrázku), bitovou kopii se měří z horní části levého horního rohu a oříznout dole.  
- Pokud (původní šířku obrázku nebo původní výška obrázku) > (požadovanou šířku obrázku / požadovaná výška obrázku), bitové kopie se měří z centra a oříznout doleva a doprava.  



Následující příklad zobrazuje na výšku obrázku, který je 225 x 300.  
  
![Původní slunečnicová image](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Následující obrázek znázorňuje obrázek snížen na 200 x 200 pomocí skrytá poměr oříznutí. Obrázek se měří od levého horního rohu výsledná v dolní části obrázku se vyhnete.  
  
![Slunečnicová image oříznout na 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Následující obrázek znázorňuje obrázek snížen na 200 × 100 pomocí skrytá poměr oříznutí. Obrázek se měří od levého horního rohu výsledná v dolní části obrázku se vyhnete.  
  
![Slunečnicová image muset oříznout, aby 200 × 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Následující obrázek znázorňuje obrázek snížen na 100 x 200 pomocí skrytá poměr oříznutí. Obrázek se měří z centra, což vede k levé a pravé části bitovou kopii se vyhnete.  
  
![Slunečnicová obrázek oříznut na 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

