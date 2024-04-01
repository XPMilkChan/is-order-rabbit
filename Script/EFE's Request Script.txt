# Please note that this script has been modified slightly to allow responses up
# to 1 MB. This does make the script slower, but it was needed to prevent crashes.
=begin
===============================================================================
 EFE's Request Script
 Version: RGSS & RGSS2 & RGSS3
 Special thanks : Ryex, Gustavo Bicalho, Kubiwa Taicho
===============================================================================
 This script will allow to request to some servers WITHOUT posting.(Only GET)
--------------------------------------------------------------------------------
Used WINAPI functions:

WinHTTPOpen
WinnHTTLConnect
WinHTTPOpenRequest
WinHTTPSendRequest
WinHTTPReceiveResponse
WinHttpQueryDataAvailable
WinHttpReadData

Call:

EFE.request(host, path, post, port)

host : "www.rpgmakervxace.net" (without http:// prefix)
path : "/forum/login.php" ( the directory path of your php file )
post : "username=kfdsfdsl&password=24324234"
port : 80 is default.

=end

module EFE
  
  # I took this method from Gustavo Bicalho's WebKit script. Special thanks him.
  def self.to_ws(str)
    str = str.to_s();
    wstr = "";
    for i in 0..str.size
      wstr += str[i,1]+"\0";
    end
    wstr += "\0";
    return wstr;
  end
  
  
  
  #EFES_WINAPI = Win32API.new('ods', 'naber', 'pp', 'p')
  WinHttpOpen = Win32API.new('winhttp','WinHttpOpen',"PIPPI",'I')
  WinHttpConnect = Win32API.new('winhttp','WinHttpConnect',"PPII",'I')
  WinHttpOpenRequest = Win32API.new('winhttp','WinHttpOpenRequest',"PPPPPII",'I')
  WinHttpSendRequest = Win32API.new('winhttp','WinHttpSendRequest',"PIIIIII",'I')
  WinHttpReceiveResponse = Win32API.new('winhttp','WinHttpReceiveResponse',"PP",'I')
  WinHttpQueryDataAvailable = Win32API.new('winhttp', 'WinHttpQueryDataAvailable', "PI", "I")
  WinHttpReadData = Win32API.new('winhttp','WinHttpReadData',"PPIP",'I')
  #WinHttpWriteData = Win32API.new('winhttp','WinHttpWriteData',"PPIP",'I')

  def self.request2(host, path, post="")
    pr = path
    if(post != "")
      pr = pr + "?" + post
    end
    pr = pr.to_s
    a = EFES_WINAPI.call(to_ws(host), to_ws(pr))
    return a
  end
  
  def self.request(host, path, post="",port=80)
    p = path
    if(post != "")
      p = p + "?" + post
    end
    p = p.to_s
    pwszUserAgent = ''
    pwszProxyName = ''
    pwszProxyBypass = ''
    httpOpen = WinHttpOpen.call(pwszUserAgent, 0, pwszProxyName, pwszProxyBypass, 0)
    if httpOpen
      httpConnect = WinHttpConnect.call(httpOpen, to_ws(host), port, 0)
      if httpConnect
        httpOpenR = WinHttpOpenRequest.call(httpConnect, nil, to_ws(p), "", '',0,0)
        if httpOpenR
          httpSendR = WinHttpSendRequest.call(httpOpenR, 0, 0 , 0, 0,0,0)
          if httpSendR
            httpReceiveR = WinHttpReceiveResponse.call(httpOpenR, nil)
            if httpReceiveR
              received = 0
              httpAvailable = WinHttpQueryDataAvailable.call(httpOpenR, received)
              if httpAvailable
                # I know this might be an ugly solution, but it does seem to be much faster than ' ' * 1048576
                ali = '                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                ' * 512 #modified this from 1024
                n = 0
                httpRead = WinHttpReadData.call(httpOpenR, ali, 1048576, o=[n].pack('i!')) # That number was 1024
                n=o.unpack('i!')[0]
                return ali[0, n]
              else
                msgbox_p("Error about query data available")
              end
            else
              msgbox_p("Error when receiving response")
            end
          else
            msgbox_p("Error when sending request")
          end
            
        else
          msgbox_p("Error when opening request")
        end
          
      else
        msgbox_p("Error when connecting to the host")
      end
        
    else
      msgbox_p("Error when opening connection")
    end
  end
end

#:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=
# WebRequest Script
#------------------------------------------------------------------------------
# Author: efeberk
#:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=
#   
#  This work is protected by the following license:
# #----------------------------------------------------------------------------
# #  
# #  Creative Commons - Attribution-NonCommercial-ShareAlike 3.0 Unported
# #  ( http://creativecommons.org/licenses/by-nc-sa/3.0/ )
# #  
# #  You are free:
# #  
# #  to Share - to copy, distribute and transmit the work
# #  to Remix - to adapt the work
# #  
# #  Under the following conditions:
# #  
# #  Attribution. You must attribute the work in the manner specified by the
# #  author or licensor (but not in any way that suggests that they endorse you
# #  or your use of the work).
# #  
# #  Noncommercial. You may not use this work for commercial purposes.
# #  
# #  Share alike. If you alter, transform, or build upon this work, you may
# #  distribute the resulting work only under the same or similar license to
# #  this one.
# #  
# #  - For any reuse or distribution, you must make clear to others the license
# #    terms of this work. The best way to do this is with a link to this web
# #    page.
# #  
# #  - Any of the above conditions can be waived if you get permission from the
# #    copyright holder.
# #  
# #  - Nothing in this license impairs or restricts the author's moral rights.
# #  
# #----------------------------------------------------------------------------
# 
#:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=

# If you find any bugs, please report them here:
# http://www.rpgmakervxace.net
# or skype: oceanjack35
#:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=:=
