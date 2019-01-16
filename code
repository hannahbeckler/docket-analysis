import urllib.request
import http.cookiejar
from collections import Counter

outfilename = "prisonercasesall.txt"
outfile = open(outfilename, "w")
otherfilename = "topdefendants.txt"
otherfile = open(otherfilename, "w")
nextfilename = "topstates.txt"
nextfile = open(nextfilename, "w")

print ('Date Filed','|','State','|','Nature of Suit','|','Plaintiff','|','Defendant',file = outfile)

def extract_info(webpage,tag,endtag):
    if newpage == "":
        infostring = ""
    else:
        infostartindex = webpage.find(tag)
        infoendindex = webpage.find(endtag,infostartindex)
        taglength = len(tag)
        if infostartindex != -1:
            infostring = webpage[infostartindex + taglength:infoendindex]
            badinfostring = infostring.find('<span id="')
            if badinfostring != -1:
                infostring = "not found"
        else:
            infostring = "not found"
    return infostring.strip();

def defend_info(webpage,tag,endtag):
    if newpage == "":
        infostring = ""
    else:
        infostartindex = webpage.find(tag)
        infoendindex = webpage.find(endtag,infostartindex)
        taglength = len(tag)
        if infostartindex != -1:
            infostring = webpage[infostartindex + taglength:infoendindex]
        else:
            infostring = "not found"
    return (', '+infostring.strip());

def tricky_extract(webpage,onetag,twotag,lasttag):
    if newpage == "":
        infostring = ""
    else:
        startindex = webpage.find(onetag)
        middleindex = webpage.find(twotag,startindex)
        lastindex = webpage.find(lasttag,middleindex)
        mtaglength = len(twotag)
        if startindex != -1:
            trickystring = webpage[middleindex + mtaglength:lastindex]
            badstring = trickystring.find('<span id="')
            if badstring != -1:
                trickystring = "not found"
        else:
            trickystring = "not found"
    return trickystring.strip();

def strip_badchars(webpage):
    badcharlist = ["\t","\n","\r","\v","\f","   ","and others",",inc.",", inc."]
    cleanpage = webpage
    for c in badcharlist:
        cleanpage = cleanpage.replace(c,"~")
    return cleanpage.replace("~","");

## MAIN BODY ##

cj = http.cookiejar.MozillaCookieJar()
opener = urllib.request.build_opener(urllib.request.HTTPCookieProcessor(cj))
opener.addheaders = [('User-agent', 'Mozilla/5.0')]

URLprefix = "https://dockets.justia.com/search?noscat=6&cases=between&sort-by-last-update=false&after=2015-1-1&before=2018-12-9&nos=550&page="
URLsuffix = "&mode=rss"
defendlist = []
statelist = []

for page in range(1,7886):
    pagestring = str(page)

    webURL = URLprefix + pagestring + URLsuffix

    try:
        infile = opener.open (webURL, timeout=15)
        newpage = infile.read().decode('utf-8')
    except:
        print ("Page not found.")
        newpage = ""

    if newpage != "":
        entrylist = newpage.lower().split('<div class="abstract">')
        listlength = len(entrylist)
        for i in range(1,listlength):
            splitpage = entrylist[i]
            cleanpage = strip_badchars(splitpage)
            date = extract_info(cleanpage,'filed: ','</')
            suit = tricky_extract(cleanpage,'nos-','>','</a>')
            plaintiff = extract_info(cleanpage,'plaintiff:</strong>','</div>')
            defend = extract_info(cleanpage,'defendant:</strong>','</div>')
            defendantstring = defend_info(cleanpage,'defendant:</strong>','</div>')
            defendantlist = defendantstring.split(', ')
            defendantlistlength = len(defendantlist)
            for m in range (1,defendantlistlength):
                defendant = defendantlist[m]
                if defendant != 'not found':
                    defendlist.append(defendant)
            state = tricky_extract(cleanpage,'/state-','>','</a>')
            if state != 'not found':
                statelist.append(state)
            print (date,'|',state,'|',suit,'|',plaintiff,'|',defend,file = outfile)
            infile.close()

    defendcount = Counter(defendlist).most_common(50)
    statecount = Counter(statelist).most_common(25)

print (defendcount,file = otherfile)
print (defendcount)
print ()
print (statecount,file=nextfile)
print (statecount)
print ()
outfile.close()
otherfile.close()
nextfile.close()
