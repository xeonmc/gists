---
title: blogtitle
author: blogauthor
---

# Blog Heading

$$e^{ix} = \cos{x} + i\sin{x}$$

Hello World



<video width="480" height="320" controls="controls">
  <source src="https://github.com/xeonmc/gists/releases/download/attach-2022-11-30-recoil/recoil.mp4" type="video/mp4">
</video>




embed:

<iframe src="https://www.desmos.com/calculator/hn1ekfjj3k?embed" width="500" height="500" style="border: 1px solid #ccc" frameborder=0></iframe>

```autoit
#NoTrayIcon
Opt('GUIOnEventMode',1)

Global Const $user32 = DllOpen('user32.dll'), $kern32 = DllOpen('Kernel32.dll')
Global Const $PERFORMANCE_FREQUENCY = DllCall($kern32, 'bool', 'QueryPerformanceFrequency', 'Int64*', Null)[1]

Global Const $CENTER_OFFSET = 200-7
Global Const $DEFAULT_DECAYRATE = 15
Global Const $DEFAULT_SPRINGRATE = 15
Global Const $DEFAULT_IMPULSE = 1000
Global Const $DEFAULT_FIRERATE = 10

Global Const $TAGINPUTMOU = 'struct;dword Type;dword Size;handle hDevice;wparam wParam;endstruct;ushort flag;ushort Alignment;ushort bflg;short bdta;ulong rbtn;long dx;long dy;ulong info;'
Global Const $SIZEINPUTMOU = DllStructGetSize(DllStructCreate($TAGINPUTMOU))
Global Const $SIZEINPUTHEAD = DllStructGetSize(DllStructCreate('struct;dword Type;dword Size;handle hDevice;wparam wParam;endstruct;'))

Global $gEta = $DEFAULT_DECAYRATE, $gOmega = $DEFAULT_SPRINGRATE, $gVx = 0, $gVy = $DEFAULT_IMPULSE, $gROF = $DEFAULT_FIRERATE

Global $hWnd = GUICreate(GetDampCase(),401,401)
Global $hEta = GUICtrlCreateInput($gEta,0,0,100,20)
Global $hOmega = GUICtrlCreateInput($gOmega,0,20,100,20)
Global $hVy = GUICtrlCreateInput($gVy,0,40,100,20)
Global $hROF = GUICtrlCreateInput($gROF,0,60,100,20)
GUICtrlCreateButton('', $CENTER_OFFSET, $CENTER_OFFSET, 15, 15)
Global $hXhair = GUICtrlCreateButton('', $CENTER_OFFSET, $CENTER_OFFSET, 15, 15)
GUICtrlCreateLabel('decay',100,0)
GUICtrlCreateLabel('spring',100,20)
GUICtrlCreateLabel('impulse',100,40)
GUICtrlCreateLabel('firerate',100,60)
GUICtrlSetOnEvent($hROF,OnInput)
GUICtrlSetOnEvent($hVy,OnInput)
GUICtrlSetOnEvent($hEta,OnInput)
GUICtrlSetOnEvent($hOmega,OnInput)
GUISetOnEvent(-3,Quit)
GUISetState()


Global $posX=0, $posY=0, $velX=0, $velY=0, $time = DllCall($kern32, 'bool', 'QueryPerformanceCounter', 'Int64*', Null)[1]

GUIRegisterMsg(0x00FF,WM_INPUT)
Local $struct = DllStructCreate('struct;ushort UsagePage;ushort Usage;dword Flags;hwnd Target;endstruct')
$struct.UsagePage=0x01
$struct.Usage=0x02
$struct.Flags=0x00000100
$struct.Target=GUICreate('')
DllCall('user32.dll', 'bool', 'RegisterRawInputDevices', 'struct*', $struct, 'uint', 1, 'uint', DllStructGetSize(DllStructCreate('struct;ushort UsagePage;ushort Usage;dword Flags;hwnd Target;endstruct')))


Main()

Func Main()
     Local $dx,$dy,$dt, $now
     While Sleep(10)
        $now = Now()
        ShotTicker($now)
        $dt = TimeDiff($now,$time) ; this needs to be done after the shot ticker updates the most recent shot
        $dx = Round(EvolvePos($dt, $posX, $velX, $gEta, $gOmega))
        $dy = Round(EvolvePos($dt, $posY, $velY, $gEta, $gOmega))
        GUICtrlSetPos($hXhair, $CENTER_OFFSET + $dx, $CENTER_OFFSET - $dy )
     WEnd
EndFunc

Func WM_INPUT($hWnd, $uMsg, $wParam, $lParam)
     Local $now = Now()
     Local $data = DllCall($user32, 'uint', 'GetRawInputData', _
                                  'handle', $lParam, _
                                    'uint', 0x10000003, _
                                 'struct*', DllStructCreate($TAGINPUTMOU), _
                                   'uint*', $SIZEINPUTMOU, _
                                    'uint', $SIZEINPUTHEAD)[3]
     If BitAnd(1,$data.bflg) And Not $wParam Then
        If Not IsFiring() Then ShotTicker($now,True)
        IsFiring(True)
     EndIf
     If BitAnd(2,$data.bflg) Then
        IsFiring(False)
     EndIf
EndFunc

Func IsFiring($change = Null)
     Local Static $state = False
     Switch $change
       Case True
            $state = True
       Case False
            $state = False
     EndSwitch
     Return $state
EndFunc

Func ShotTicker($now, $onClick=False)
     Local $iX = 0;0.5*sqrt(0.5*($gEta*$gEta + $gOmega*$gOmega))*$refState[1]*sin(-$refState[0]+$refState[1]) ; not quite right since this uses the position now for each shot
     Local $iY = $gVy
     If $onClick Then
        Local $refState = CalcState($now)
        If TimeDiff($now,$time) >= 1/$gROF Then
           $refState[2] += $iX
           $refState[3] += $iY
           SetState($refState)
        EndIf
     ElseIf IsFiring() Then
        Local $ct = Floor(TimeDiff($now,$time)*$gROF)
        If $ct<1 Then Return
        Local $dt = $ct/$gROF, $dtick = SecToTick($dt)
        Local $refState = CalcState($time+$dtick)
        Local $rx[$ct],$ry[$ct],$vx[$ct],$vy[$ct]
        For $i=0 to $ct-1
            Local $tau = ($ct-$i-1)/$gROF
            $rx[$i] = EvolvePos($tau, 0, $iX, $gEta, $gOmega)
            $ry[$i] = EvolvePos($tau, 0, $iY, $gEta, $gOmega)
            $vx[$i] = EvolveVel($tau, 0, $iX, $gEta, $gOmega)
            $vy[$i] = EvolveVel($tau, 0, $iY, $gEta, $gOmega)
        Next
        For $i=0 to $ct-1
            $refState[0] += $rx[$i]
            $refState[1] += $ry[$i]
            $refState[2] += $vx[$i]
            $refState[3] += $vy[$i]
        Next
        SetState($refState)
     Else
        Local $refState = CalcState($now)
        Local $U = ( $refState[0]*$refState[0] + $refState[1]*$refState[1] ) * $gOmega*$gOmega
        Local $T = ( $refState[2]*$refState[2] + $refState[3]*$refState[3] ) / 2
        If $U + $T < 0.000001 Then
           $refState[0] = 0
           $refState[1] = 0
           $refState[2] = 0
           $refState[3] = 0
           SetState($refState)
        EndIf
     EndIf
EndFunc

Func OnInput()
     Local $val = GUICtrlRead(@GUI_CtrlId)
     Switch @GUI_CtrlId
       Case $hROF
            $gROF = ( $val>0 ? $val : $DEFAULT_FIRERATE )
            GUICtrlSetData($hROF,$gROF)
       Case $hVy
            $gVy = ( $val>=0 ? $val : $DEFAULT_IMPULSE )
            GUICtrlSetData($hVy,$gVy)
       Case $hEta
            $gEta = ( $val>=0 ? $val : $DEFAULT_DECAYRATE )
            GUICtrlSetData($hEta,$gEta)
       Case $hOmega
            $gOmega = ( $val>=0 ? Number($val) : $DEFAULT_SPRINGRATE )
            GUICtrlSetData($hOmega,$gOmega)
     EndSwitch
     ClearState()
     WinSetTitle($hWnd,'',GetDampCase())
EndFunc

Func GetDampCase()
     Local $delta = $gEta*$gEta-$gOmega*$gOmega, $case = ( $delta = 0 ? 'Critically Damped' : ( $delta>0 ? 'Overdamped' : 'Underdamped') ) 
     Return $case
EndFunc


Func Quit()
     Exit
EndFunc

Func ClearState()
     $posX = 0
     $posY = 0
     $velX = 0
     $velY = 0
EndFunc

Func CalcState($now) ; evolves over dt based on last set value
     Local $dt = TimeDiff($now,$time)
     Local $aState = [ _
       EvolvePos($dt, $posX, $velX, $gEta, $gOmega), _
       EvolvePos($dt, $posY, $velY, $gEta, $gOmega), _
       EvolveVel($dt, $posX, $velX, $gEta, $gOmega), _
       EvolveVel($dt, $posY, $velY, $gEta, $gOmega), _
       $now _
     ]
     Return $aState
EndFunc

Func SetState($newState)
     Local Enum $rx, $ry, $vx, $vy, $ts
     $posX = $newState[$rx]
     $posY = $newState[$ry]
     $velX = $newState[$vx]
     $velY = $newState[$vy]
     $time = $newState[$ts]
EndFunc

Func Now()
     Return DllCall($kern32, 'bool', 'QueryPerformanceCounter', 'Int64*', Null)[1]
EndFunc

Func TimeDiff($tNew,$tOld)
     Return ($tNew-$tOld)/$PERFORMANCE_FREQUENCY
EndFunc

Func SecToTick($sec)
     Return Round($sec*$PERFORMANCE_FREQUENCY)
EndFunc

Func EvolvePos($dt, $oldPos, $oldVel, $eta, $omega)
     Local $delta = $eta*$eta - $omega*$omega
     Return exp(-$dt*$eta) * _
            ( M($dt,$delta)*$oldPos _
            + N($dt,$delta)*($oldPos*$eta + $oldVel) )
EndFunc

Func EvolveVel($dt, $oldPos, $oldVel, $eta, $omega)
     Local $delta = $eta*$eta - $omega*$omega
     Return exp(-$dt*$eta) * _
            ( M($dt,$delta)*$oldVel _
            - N($dt,$delta)*( $oldVel*$eta + $oldPos*$omega*$omega ) )
EndFunc

Func N($x,$d)
     Local $z = $d*$d
     If $d=0 Then
        Return $x
     ElseIf $d>0 Then
        Local $z = sqrt($d)
        Return ( exp($x*$z)-exp(-$x*$z) ) / (2*$z)
     ElseIf $d<0 Then
        Local $z = sqrt(-$d)
        Return sin($x*$z) / $z
     Else
        Return SetError(1,0,"can't take complex")
     EndIf
EndFunc

Func M($x, $d)
     If $d=0 Then
        Return 1
     ElseIf $d>0 Then
        Local $z = sqrt($d)
        Return ( exp($x*$z)+exp(-$x*$z) ) / 2
     ElseIf $d<0 Then
        Local $z = sqrt(-$d)
        Return cos($x*$z)
     Else
        Return SetError(1,0,"can't take complex")
     EndIf
EndFunc
````
