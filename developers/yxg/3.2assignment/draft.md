ͳ�ƵĽ���ҷ�����ͬĿ¼�µ�log�ļ���  
����ÿһ��ͳ�ƽ���Ҷ�����Ӧ��˵��

# patch type
![patch type](/developers/yxg/3.2assignment/image/patch-type.png)
- ���ȣ�percentage breaks down to similar range��仰������Ȼ��feature��bugռ���˾�����������Ǳ����仯�ܴ󣬿��ǵ�����ʱ��performance��feature���𲻴󣬽����ߺϲ��󣬹��Ʊ�׼����С��
- ���⣬�����д����Ļع����ŵ���feature�У����ⲿ�ִ��ó�����������������feature��feature����û����ô��

# bug, performance, feature
- ��������û�л�ͼ
  - performance�������������ٵ��Ƿ���ܶ࣬��״ͼ���������������ԣ�ͼ���������ң�����ֱ�����ɱ��
  - feature�Ļ������ǣ�rtsupport�ھ�������İ汾��ռ���˾�����������������Ƿ���̫�࣬ÿ��ֻ�к��ٵļ�������ͼ��ȫû������
- ����bug
![bug](/developers/yxg/3.2assignment/image/bug-type.png)
���Եķ�����Կ���semantic��concurrencyռ���˾������
![bug-detail](/developers/yxg/3.2assignment/image/bug-type-other.png)
ϸ��֮��ռ�Ƚϴ��������semantics��compiling_err��deadlock��irq/softirq���ֱ�λ��semantics��concurrency��error code�С�  
����ͬ��������̫ϸ��������ѡ����5���������࣬ͼ��Ȼ����  
��FAST'13�ж�ϸ�ֵ�bug/bugtype����ͼ���ҷ����Ǹ�����semantic����һ��  
![semantic](/developers/yxg/3.2assignment/image/semantics.png)
����һ��  

��һ�������ǣ��кܶ��bug�����������ٿ����ǲ��ǿ��Ժϲ���
na, config_err, overflow, err_access, typo_var, resourse_leak

���ڵ��������
- �������ͼ����������ô�ң�������ܹ��ϲ�performance��feature�е����࣬�ϲ���4-5�����
- bug��һЩ���ֽ��ٵ�type�����ſ���һ�£������з�����������߲��Ǻܱ�Ҫ�����һ����������