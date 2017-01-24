# -XML-Chapter-3.3-Exercises-for-Data-Science-Intensive-Class

Answers read better in edit mode


# 1. 10 countries with the lowest infant mortality rates					
import pandas as pd					
document = ET.parse( './data/mondial_database.xml' )					
df = pd.DataFrame({'country': [' '], 'infant_mortality': [1.0]})					
x=0					
for element in document.iterfind('country'):					
    df.ix[x,'country'] = element.find('name').text					
    if element.find('infant_mortality') is None:					
        #print('element not found, or element has no subelements')					
        df.ix[x,'infant_mortality'] = 999.0					
    else:					
        df.ix[x,'infant_mortality'] = float(element.find('infant_mortality').text)					
    x +=1					
df.sort_values(by = 'infant_mortality').head(10)					
					
	infant_mortality	name			
38	1.81	Monaco			
98	2.13	Japan			
117	2.48	Bermuda			
36	2.48	Norway			
106	2.53	Singapore			
37	2.6	Sweden			
10	2.63	Czech Republic			
78	2.73	Hong Kong			
79	3.13	Macao			
44	3.15	Iceland			
					
					
					



# 2. 10 cities with the largest population						
import pandas as pd						
document = ET.parse( './data/mondial_database.xml' )						
df = pd.DataFrame({'country': [' '], 'city': [' '], 'population': [0]})						
x=0						
for element in document.iterfind('country'):						
    for subelement in element.getiterator('city'):						
        df.ix[x,'country'] = element.find('name').text						
        df.ix[x,'city'] = subelement.find('name').text						
        if subelement.find('population') is None:						
            df.ix[x,'population'] = 0						
        else:						
            for z in subelement.getiterator('population'):						
                df.ix[x,'population'] = float(z.text)  #save last collected population number						
        x +=1						
df.sort_values(by='population', ascending=False).head(10)						
						
						
	city	country	population			
1341	Shanghai	China	22315474			
771	Istanbul	Turkey	13710512			
1527	Mumbai	India	12442373			
479	Moskva	Russia	11979529			
1340	Beijing	China	11716620			
2810	São Paulo	Brazil	11152344			
1342	Tianjin	China	11090314			
1064	Guangzhou	China	11071424			
1582	Delhi	India	11034555			
1067	Shenzhen	China	10358381			
						




# 3. 10 ethnic groups with the largest overall populations (sum of best/latest estimates over all countries)							
import pandas as pd							
document = ET.parse( './data/mondial_database.xml' )							
df = pd.DataFrame({'country':[' '],'population':[0],'ethnicity':[' '],'eth_percent':[0.0],'eth_population':[0]})							
x=0							
for element in document.iterfind('country'):							
    df.ix[x,'country'] = element.find('name').text							
    if element.find('population') is None:							
        df.ix[x,'population'] = 0							
    else:							
        for subelement in element.iterfind('population'):  #just iterators at the top level, no subnodes							
            df.ix[x,'population'] = float(subelement.text)  #save last collected population number							
        for subelement in element.iterfind('ethnicgroup'):							
            df.ix[x,'ethnicity'] = subelement.text							
            df.ix[x,'eth_percent'] = float(subelement.attrib['percentage']) #'ethnicgroup percentage='							
            #print(subelement.attrib['percentage'])							
            df.ix[x,'eth_population'] = int(df.ix[x,'population'] * df.ix[x,'eth_percent'] / 100)							
            x +=1							
            df.ix[x,'country'] = df.ix[(x-1),'country']							
            df.ix[x,'population'] = df.ix[(x-1),'population']							
df.head(20)							
df.groupby('ethnicity')['eth_population'].sum().sort_values(ascending=False).head(10)							
							
	country	eth_percent	eth_population	ethnicity	population		
0	Albania	95	2660131	Albanian	2800138		
1	Albania	3	84004	Greek	2800138		
2	Greece	93	10059145	Greek	10816286		
3	Macedonia	64.2	1322387	Macedonian	2059794		
4	Macedonia	25.2	519068	Albanian	2059794		
5	Macedonia	3.9	80331	Turkish	2059794		
6	Macedonia	2.7	55614	Gypsy	2059794		
7	Macedonia	1.8	37076	Serb	2059794		
8	Serbia	82.9	5903032	Serb	7120666		
9	Serbia	0.9	64085	Montenegrin	7120666		
10	Serbia	3.9	277705	Hungarian	7120666		
11	Serbia	1.4	99689	Roma	7120666		
12	Serbia	1.8	128171	Bosniak	7120666		
13	Serbia	1.1	78327	Croat	7120666		
14	Montenegro	43	266612	Montenegrin	620029		
15	Montenegro	32	198409	Serb	620029		
16	Montenegro	8	49602	Bosniak	620029		
17	Montenegro	5	31001	Albanian	620029		
18	Kosovo	92	1595162	Albanian	1733872		
19	Kosovo	5	86693	Serbian	1733872		
							
ethnicity							
Han Chinese    1.245059e+09							
Indo-Aryan     8.718156e+08							
European       4.948722e+08							
African        3.183251e+08							
Dravidian      3.027137e+08							
Mestizo        1.577343e+08							
Bengali        1.467769e+08							
Russian        1.318570e+08							
Japanese       1.265342e+08							
Malay          1.219935e+08							
							






# 4. name and country of a) longest river, b) largest lake and c) airport at highest elevation			
			
import pandas as pd			
document = ET.parse( './data/mondial_database.xml' )			
df_rivers = pd.DataFrame({'country':[' '],'river':[' '],'length':[0.0]})			
df_lakes = pd.DataFrame({'country':[' '],'lake':[' '],'area':[0.0]})			
df_airports = pd.DataFrame({'country':[' '],'airport':[' '],'elevation':[0]})			
			
x=0			
for element in document.iterfind('river'):			
    df_rivers.ix[x,'river'] = element.find('name').text			
    df_rivers.ix[x,'country'] = element.attrib['country']			
    if element.find('length') is None :			
        df_rivers.ix[x,'length'] = 0.0			
    else :			
        df_rivers.ix[x,'length'] = float(element.find('length').text)			
    x +=1			
			
x=0			
for element in document.iterfind('lake'):			
    df_lakes.ix[x,'lake'] = element.find('name').text			
    df_lakes.ix[x,'country'] = element.attrib['country']			
    if element.find('area') is None :			
        df_lakes.ix[x,'area'] = 0.0			
    else :			
        df_lakes.ix[x,'area'] = float(element.find('area').text)			
    x +=1			
    			
x=0			
for element in document.iterfind('airport'):			
    df_airports.ix[x,'airport'] = element.find('name').text			
    df_airports.ix[x,'country'] = element.attrib['country']			
    temp = element.find('elevation').text			
    if temp is None :			
        df_airports.ix[x,'elevation'] = -999			
    else :			
        df_airports.ix[x,'elevation'] = int(temp)			
    x +=1			
    			
			
df_rivers.sort_values(by='length', ascending=False).head(5)          # top 5 rivers in length			
df_lakes.sort_values(by='area', ascending=False).head(5)             # top 5 lakes in area			
df_airports.sort_values(by='elevation', ascending=False).head(5)     # top 5 airport in elevation			
			
			
	country	length	river
174	CO BR PE	6448	Amazonas
137	CN	6380	Jangtse
136	CN	4845	Hwangho
123	R	4400	Lena
205	RCB ZRE	4374	Zaire
			
	area	country	lake
54	386400	R AZ KAZ IR TM	Caspian Sea
109	82103	CDN USA	Lake Superior
81	68870	EAT EAK EAU	Lake Victoria
106	59600	CDN USA	Lake Huron
108	57800	USA	Lake Michigan
			
	airport	country	elevation
80	El Alto Intl	BOL	4063
219	Lhasa-Gonggar	CN	4005
241	Yushu Batang	CN	3963
813	Juliaca	PE	3827
815	Teniente Alejandro Velasco Astete Intl	PE	3311





