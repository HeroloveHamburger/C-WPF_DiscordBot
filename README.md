def resv_Pack_key(a):
    #print('----')
    #print(a)
    start = 5
    a_int = int(a[4],16)
    a_int = a_int >> 1
    a_int = a_int & 3
    for i in range(1,4):
        #print("---start---")
        #print(start)
        #print("---num---")
        #print(i)
        #print("---before---")
        numa_int = a_int +i-1 & 3
        #print(numa_int)
        #print('--decode--')
        #print('and 3 :' +str(numa_int))
        #原來位置
        temp_dl = a[start] 
        #替換位置
        blInt = start+numa_int+1
        temp_bl = a[blInt] 
        #print("need change : " + str(temp_dl))
        #print("change_index :" + str(blInt))
        #print("change :"  + str(a[blInt]))
        
        a[start] = temp_bl
        a[blInt] = temp_dl
        start = start+5
        #print('--decode--')
    return a

def resv_Pack(b):
    global last_word,txthex
    reverseP = b
    reverseP.reverse()
    #print(reverseP)
    #print("長度")
    #print(int(reverseP[-2],16)-12)
    lent = int(reverseP[-2],16)-12
    #print(b)
    pack2 = b[4:lent-6]
    #print(pack2)
    #packtest.insert(len(packtest),"00")
    pack2 =  HexString_to_List(pack2)
    #print(pack2)
    pack =  HexString_to_List(b)
    txthex = []
    #print("pack")
    #print( pack)
    #print(b)
    b.reverse()

    b = b[4:8]
    
    #print(b)
    
    b.reverse()

    key = ''
    
    for i in b:
        key = key+ i

    key_int = int(key,16)
    #print('key : ' +key)
    key_int = key_int + 4660


    ebx = key_int*key_int

    ebx_hex = hex(ebx)

    ebx_hex = ebx_hex[len(ebx_hex)-4:len(ebx_hex)]
    #print("pack[2:-11]")
    #print(pack[2:-11])
    #pack2 = pack[2:-11]
    pack2.reverse()
    #print("pack2")
    #print(pack2)
    pack_len = len(pack2)

    nbits = 16

    last_word =''
    #print("Pack_Len")
    #print(pack_len)
    oplen = 0
    for i in range(0,pack_len):
        #print(i)
        
        unpackint =int(pack2[i-1],16)
        
        unpackint = unpackint >> 2
        
        unpackhex = hex(unpackint)
        
        #print(unpackhex)
        unpack_key_int = key_int+ (i+1)*3
        #print(unpack_key_int)
        unpack_key_hex = hex(unpack_key_int)
        
        unpack_key_hex = unpack_key_hex[len(unpack_key_hex)-4:len(unpack_key_hex)]
        #print(unpack_key_hex)
        test  =0
        
        if i ==0:
            test =int(pack2[i],16) - int(ebx_hex,16)
            #print(test)
        else:
            #print(" i - (i-1) :" + str(int(pack2[i],16)) +"-"+ str(int(unpackhex,16)))
            test = int(pack2[i],16) - int(unpackhex,16)
        

        origin = int('{:04X}'.format(test & ((1 << nbits)-1)),nbits)
        if oplen ==7:
            oplen = 0
        left = 16-oplen-1
        right = oplen+1
        oplen = oplen+1
        #print('Left:'+str(left))
        #print('Right:'+str(right))
        if left ==8:
            left = 15
            right = 1
        leftuse = 0xFFFF << left
        rightuse = 0xFFFF >> right

        A = origin & leftuse
        B = origin & rightuse


        A = A >> left
        B = B << right

        C = A | B
        #print(hex(C))
        txtInt = C + int(unpack_key_hex,16)
        txt = hex(int('{:04X}'.format(txtInt & ((1 << nbits)-1)),nbits))
        #print(txt)
        txt =txt.replace('0x','')
        #print(len(txt))
        txtLen = len(txt)
        
        if txtLen ==4:
            txt = txt[2:4] + txt[0:2]
        if i == pack_len-1:
            #print("laset_txt")
            #print(txt)
            www = ""
        else:
            #print(txt)
            txt =txt.replace('00','')
        
        if i ==0:
            txt = txt[2:4]
        #print(txt)
        #print(''.join([chr(int(''.join(c), 16)) for c in zip(txt[0::2],txt[1::2])]))
        if txt.find("00") ==-1:
            txthex.append(txt)
            last_word = last_word + ''.join([chr(int(''.join(c), 16)) for c in zip(txt[0::2],txt[1::2])])
    #print("----Packet UnPack----")
    #print(txthex)
    print("帳號密碼: "+last_word)
    return last_word
    #print("---------------------")
