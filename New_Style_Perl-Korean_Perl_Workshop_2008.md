```text
TITLE::최신 스타일 Perl로 개과천선하기

최신 스타일 Perl로
개과천선하기

     Korean Perl Workshop 2008     
aero
--------------------------------------------------
Perl의 역사 20여년

1.000          1987-Dec-18
2.000          1988-Jun-05
3.000          1989-Oct-18
4.000          1991-Mar-21
5.000          1994-Oct-17
5.002          1996-Feb-29
5.004          1997-May-15
5.6.0          2000-Mar-22
5.8.0          2002-Jul-18
5.8.8          2006-Jan-31
5.10.0         2007-Dec-18
--------------------------------------------------
Perl 6는 언제?

[☃크리스마스에 나옵니다.☃]
--------------------------------------------------

Perl 6나오면 Perl 5 배운 건 말짱 도루묵?

[☞ Perl 5 도 충분히 강력하며 쓸만하다.]

[☞ Perl 5 에 Perl 6의 기능이 Backport되고 있다.]

[☞ Perl 5 와 6는 같이 쓰일 수 있도록 개발 중]

[☞ 철학과 문화를 공유하기 때문에 걱정 뚝!]
--------------------------------------------------
10년이면 강산도 변한다.
[20년 된 Perl도 변했다.]

호랑이는 죽어서 가죽을 남기고
[Perl은 오래된 책,문서를 남겼다.]
--------------------------------------------------
改過遷善
--------------------------------------------------
Perl is NOT CGI
--------------------------------------------------


use strict;
를 쓰자

--------------------------------------------------
use strict;

perl 5.0에서 도입

use strict 'vars';
use strict 'refs';
use strict 'subs';

use strict qw/vars refs subs/;
--------------------------------------------------
use strict 'vars';

모든 변수는 our,my,local을
사용하여 변수의 영역을 사용 전에
명시적으로 선언해야 한다.
--------------------------------------------------
Before


#!/usr/bin/perl               
$num = 10;
print $nun; # No error


After


#!/usr/bin/perl               
use strict;
my $num = 10;
print $nun; # Error


--------------------------------------------------
use strict 'refs';

심볼릭 레퍼런스를 사용하지 못한다.
--------------------------------------------------
Before


#!/usr/bin/perl                    
$var = 10;
$symname = 'var';  # 심볼릭 레퍼런스
print $$symname;


After


#!/usr/bin/perl                    
use strict;                  
my $var = 10;
my $ref = \$var;   # 하드 레퍼런스
print $$ref;


--------------------------------------------------
use strict 'subs';

서브루틴이름으로 정의되어 있지 않은
bareword를 사용하지 못한다.
--------------------------------------------------
Before


#!/usr/bin/perl                         
sub hello { print "hello\n" }
helle;      # No error
$str = hi;  # bareword가 문자열화 됨
print "$str\n";


After


#!/usr/bin/perl                         
use strict;
sub hello { print "hello\n" }
hello();
my $str = 'hi';
print "$str\n";


--------------------------------------------------


use warnings;
를 쓰자

--------------------------------------------------



#!/usr/bin/perl              
use warnings;
$a + 0; 



☞  Useless use of addition (+) in void context
☞  Name "main::a" used only once: possible typo 
☞  Use of uninitialized value $a in addition (+)

다양한 문제 발생 가능성을 경고해준다.
--------------------------------------------------

OLD


#!/usr/bin/perl -w
또는
$^W=1;


NEW ( Perl 5.6 이후 )


#!/usr/bin/perl
use warnings;



☞ -w 옵션은 실행 시 모든 코드에 영향을 미치며
☞ use warnings는 렉시컬 영역에만 영향을 미친다.
--------------------------------------------------


use strict;  use warnings;
는 렉시컬 영역이다 따라서 특정 렉시컬 영역에서 비활성화 시킬 수 있다.

:
#!/usr/bin/perl
use strict;
use warnings;
...
while (<>) {
    no strict 'refs';
    ....
}

{
    no warnings;
    ....
}


--------------------------------------------------



#!/usr/bin/perl
use strict;
use warnings;
...


☺ 개과천선의 지름길 ☺
--------------------------------------------------


our
my
local
을 알고 쓰자!

--------------------------------------------------

렉시컬 영역(lexical scope), 환경(environment)



#!/usr/bin/perl                             
# env 1
{
   # env 2
   
}
# env 1
while (<>) {
   # env 3
}
# env 1


☞  블럭{}으로 둘러싸여진 코드블록(조건문,루프,서브루틴 등)
     eval,do,require,use에 의해 포함되는 코드,파일 등은
     독립적인 렉시컬 영역을 가진다.
☞  코드의 제일 바깥쪽은 그 파일 전체가 된다.
--------------------------------------------------

패키지(package) = 이름공간(namespace)

☞  패키지를 지정하지 않으면 기본으로 main패키지이다.


#!/usr/bin/perl
$a=2;
print $a; # 2가 찍힘 같은 패키지 안에서는 그냥 쓸 수 있다.
print $main::a;  # 2가 찍힘


☞  타 패키지의 변수에 접근하려면 명시적으로 적어줘야 한다.


#!/usr/bin/perl
package Mytest;
$a=2;
package main;
print $Mytest::a


--------------------------------------------------

패키지는 다음 패키지선언을 만나거나 해당 패키지가 선언된
렉시컬 영역이 끝나면 끝난다.


package Mytest1;                     
# 1
package Mytest2;
# 2
{
    # 2
    package Mytest3;
    # 3
}
# 2


--------------------------------------------------

our


#!/usr/bin/perl                   |  #!/usr/bin/perl         
use strict;                       |  use strict;
use warnings;                     |  use warnings;
use vars qw/$var2/;               |  our $var = 1;
                                  |  {
our $var = 2;                     |     package Mytest;
$var2 = 3;                        |     our $var = 2;
print $var,$var2,"\n"; # 2 3      |  }
                                  |  print $var,"\n"; # 1 $main::var
package Mytest;                   |  print $Mytest::var,"\n"; # 2
print $var,$main::var2,"\n" # 2 3 |


☞  our는 패키지 변수이다.
☞  use vars로 패키지 변수를 미리 지정할 수 있다. (our는 Perl 5.6에서 도입)
☞  패키지 변수는 패키지의 심볼테이블(Symbol Table)에 저장된다.
☞  our는 선언된 렉시컬 영역 내에서는 이름만으로 접근가능하다.
☞  our가 선언된 렉시컬 영역 외부에서 접근하려면 패키지를 명시적으로 지정해야 한다.
--------------------------------------------------

my


#!/usr/bin/perl                              
use strict;

my $var = 1;
print $var,"\n"; # 1
foreach my $var (2..3) {
    print $var,"\n"; # 2~3
    prt(); # 1
}
print $var,"\n"; # 1

sub prt { print $var,"\n" }


☞  my는 선언된 렉시컬 영역의 독립적인 스크래치패드(Scratchpad)라는 곳에 저장
☞  my는 해당 렉시컬 영역 내에서만 유효하다.(심볼테이블과 상관없음) 외부에서 보지 못한다.
☞  파일전체 렉시컬 영역을 대상으로 사용할 변수는 굳이 our로 선언해서 쓸 필요가 없다.
    (our는 모듈 외부에서 참조해야 하는 변수(버전정보 등) 이외에 사용할 일이 거의 없다.)
--------------------------------------------------

local


#!/use/bin/perl                                 
use strict;

our $var = 1;
print $var,"\n"; # 1
{
    local $var = 2;
    print $var,"\n"; # 2
    prt(); # 2
}
print $var,"\n"; # 1
sub prt { print $var, "\n" }


☞  local은 선언된 렉시컬 영역내에서 심볼테이블의 원래 값을 히든스텍(hidden stack)에
    백업해놓고 자신이 패키지 변수인 것처럼 동작하며 해당 영역을 빠져나가면 원래 값이 복원된다.
☞  local은 같은 이름의 패키지 변수가 이전에 선언되어 있어야 사용  가능하다.
    ( 위에서 our $var = 1;을 my $var = 1; 로 하면 동작안함 )
☞  현대적 Perl에서는 몇몇 특수한 경우를 제외하고 local을 쓸 일이 별로 없다. 대부분 my로 충분하다.


my $content = do { local $/; <$fh> };  # File Slurp


--------------------------------------------------


3개의 인자 형식 open과
렉시컬 파일핸들을 사용하자.

--------------------------------------------------

OLD


open FH, '>file.txt';
print FH "hello\n";


NEW


open my $fh, '>', 'file.txt';
print {$fh} "hello\n";



☞  객체지향적 파일 인터페이스를 원하면 IO::File 혹은 Path::Class 모듈 사용
--------------------------------------------------

open.pl


...
open FH, $ARGV[0];
...


perl open.pl '| rm -rf /' 로 실행시키면?

현 디렉토리에 '| rm -rf /' 라는 파일을 touch '| rm -rf /' 로 만들고
2인자 형식 open으로 넘어온 파일리스트를 차례로 열어 처리하는 스크립트를
perl open2.pl * 로 실행시키면?
--------------------------------------------------
T_T
--------------------------------------------------

파일핸들을 서브루틴에 넘기기

OLD


open FH, '<file.txt';                        
test(*FH);

sub test {
    local *FH = shift;
    while (<FH>) {
        print;
    }
}


NEW


open my $fh, '<', 'file.txt';                
test($fh);

sub test {
    my $fh = shift;
    while (<$fh>) {
        print;
    }
}


--------------------------------------------------


서브루틴 호출시 &를
습관적으로 붙이지 말자

--------------------------------------------------



OLD                     NEW

&foo()                  foo()
&foo($arg1, $arg2)      foo($arg1, $arg2)
&foo                    foo(@_)
&$subref()              $subref->()
&$subref($arg1, $arg2)  $subref->($arg1, $arg2)
&$subref                $subref->(@_)



☞  &을 붙여서 호출하면 서브루틴의 프로토타입을 무시한다.
☞  &을 붙이고 ()를 안 쓰고 호출하면 기본으로 @_를 인자로 사용
--------------------------------------------------

&을 사용해야 할 때(서브루틴 호출과 무관)


☞ 서브루틴이 정의되어 있는지 채크할때

 defined &foo

☞ 서브루틴 정의를 없앨 때

 undef &foo


☞ 서브루틴의 레퍼런스에 접근할 때

 \\&foo

--------------------------------------------------


괄호를
적절히 쓰자

--------------------------------------------------

함수

☞  Perl에서 함수는 실제로 연산자일 뿐이다.

☞  하나의 인자를 기대하는 이름있는 단항연산자.( undef, defined 등..)
  if ( defined $var ) { print "defined\n" }

☞  여러개의 인자(LIST)를 기대하는 리스트연산자.(print, sort 등 )
  print 1,2,3,4;

☞  연산자는 우선순위에 문제가 없을 경우 최우선으로 인자(피연산자)들과 결합한다.
  print LIST  <- 문법적 정의
  print 1,2,3,4;  # 괄호가 없어도 1,2,3,4가 리스트로 구성된다.
  print(1,2,3,4); # 괄호는 있어도 상관없다.(인자 범위의 명시적 지정일 뿐)
--------------------------------------------------

☞ Perl내부 함수와 CORE모듈로 자주 쓰이는 함수는 기본적으로 함수호출시
    괄호를 쓰지말 것.

☞ 우선순위에 문제가 있거나 혼돈이 있을 경우 경우 괄호를 사용
 print (1+2),4;    # 3만 찍힘 괄호를 인자들(LIST)을 묶는 괄호로 인식
 print( (1+2),4 ); # 3 4 찍힘
 
 print join '-', @a,"\n";   # @a,"\n" 이 하나의 리스트로 join함수에 전달
 print join('-', @a),"\n";  # @a를 join후 "\n"을 더한 리스트가 print에 전달

☞ 그 외 다른 모듈과 사용자 정의 서브루틴은 반드시 괄호를 붙여서 호출
  (단 함수명과 괄호 사이는 공백이 없도록 조건문,루프에 쓰이는 if,for등의
    뒤의 괄호는 공백을 띄워서 쓰는 것과 구별)
--------------------------------------------------

Before


open( my $fh, '<', 'file.txt' ) || "Can't open $!";
while(<$fh>) {
    my @words = split( ' ', $_);
    print( join( '-', @word), "\n" );
}
close($fh);


After


open my $fh, '<', 'file.txt' or "Can't open $!";
while (<$fh>) {
    my @words = split ' ',$_;
    print join( '-', @word), "\n";
}
close $fh;


--------------------------------------------------

조건연산자는
&&,||,! 만 쓰자

--------------------------------------------------

Before


if ( $a > 1 and $a < 5 ) { ... }
if ( not defined $a ) { ... }


After


if ( $a > 1 && $a < 5 ) { ... }
if ( ! defnied $a ) { ... }



☞ 조건 연산에서 not,and,or는 !,&&,||로만 사용하자.
☞ 두 부류는 연산자 우선순위만 다르고 동일한 기능
--------------------------------------------------

단 or은 다음과 같은 경우에만 사용


open my $fh, '<', 'file.txt' or die "Can't open $!";



주의!


open my $fh, '<', 'file.txt' || die "Can't open $!";    # X
open( my $fh, '<', 'file.txt' ) || die "Can't open $!"; # O



--------------------------------------------------


설정을 코드로
사용하지 말자

--------------------------------------------------

<예>


<config.pl>                                
$home = '/home/user/';
$shell = 'bash';
...

<main.pl>
require 'config.pl'
...



☞  use strict;하에서 사용하지 못하며 피해야 할 전형적 old perl style
☞  설정은 일반 텍스트로 만들고 읽어들여 파싱해서 해쉬에 넣어쓰거나
☞  CPAN의 Config::* 류 모듈을 사용하자.
--------------------------------------------------


외부 라이브러리는
모듈을 만들자

--------------------------------------------------

<예>


<Mylib.pl>                   | <Mylib.pm>
sub test { print "test\n"}   | package Mylib;
1;                           | use base qw/Exporter/;
                             | our @EXPORT = qw/test/;
                             | sub test { print "test\n" }
                             | 1;
                             |
<main.pl>                    | <main.pl>
require 'Mylib.pl'           | use Mylib;
test();                      | test();



☞  모듈은 만들기 어렵지 않다.
☞  use는 require와 import를 동시에 해주는 것이다.
☞  package(namespace)는 폼으로 만들어 놓은 것이 아니다.
    ( main 패키지의 품에서 벗어나 보자 )
--------------------------------------------------

OOP를
두려워 말자.

--------------------------------------------------



package Man;
use strict;
use warnings;

sub new {                        # Perl에서 new는 키워드가 아니다.
    my ($class, $age) = @_;      # $class는 "Man", $age는 12
    my $self = { age => $age };  # 해쉬레퍼런스
    bless $self, $class;         # 레퍼런스에 객체이름을 붙인다.
}

sub add_age {
    my ($self, $num) = @_;       # $self는 $obj, $num은 3
    $self->{age} += $num;
    return $self->{age};
}

1;




use Man;

my $obj = Man->new(12);     #첫번째 인자로 Man이 넘어간다.
print $obj->add_age(3),"\n"; #첫번째 인자로 $obj가 넘어간다.



☞  Perl객체는 package를 기반으로 구현된다.
☞  Moose http://search.cpan.org/dist/Moose/ , InsideOut 객체등 다른 대안 존재
--------------------------------------------------

좋은 코딩스타일을 본받자!
[[img src="PBP.jpg" width="300" height="400"]]
--------------------------------------------------
자동 코드정렬
perltidy http://search.cpan.org/dist/Perl-Tidy/

PBP 규칙 자동채크
Perl::Critic http://search.cpan.org/dist/Perl-Critic/
--------------------------------------------------
최신 경향 Perl 교과서
Plagger http://plagger.org/trac
--------------------------------------------------
```

최신정보(2011-05-06 갱신)

```
Perl은 역사가 오래된 만큼 과거의 Legacy한 코드들과 문서들이 많이 남아있어 그런걸 보고 공부하신분들은
아직도 철지난 스타일로 코딩하시는 경우를 많이 봅니다.

대표적인것을 들자면 use strict; 안쓰는 것과
open FH, '<somefile.txt'; 같이 bareword파일핸들에 2인자 형식 open문을 쓰는 것이죠.

그래서 소위 Modern Perl이라면 어떤식으로 코딩해야 하는지 가이드 라인이 될만한 문서를 소개합니다.

Perl코딩시 하지 말아야 할 것
http://me.veekun.com/blog/2011/04/13/perl-worst-practices/ (번역: http://cafe.naver.com/perlstudy/655 )
http://perl-begin.org/tutorials/bad-elements/

현대적 Perl OOP에 대한 가이드라인
http://urth.org/~autarch/new-pod/html/perlootut.pod.html
https://docs.google.com/viewer?a=v&pid=explorer&chrome=true&srcid=1bUdjm4YzxlLhopigzO0drUOiGYm7SxjuiSn0FJCaS-AYXuaF5Uvqcpv-BAUk&hl=ko

Modern Perl 무료 책
http://onyxneon.com/books/modern_perl/

Learning Perl 5판 번역서 비평
https://github.com/aero/perl_docs/wiki/Learning-perl-5th-review
```

