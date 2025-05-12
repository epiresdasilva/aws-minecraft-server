# Minecraft Server on AWS

My 7 years old son is a fan of Minecraft. He started playing last year (2023) and just talk about that. Now, he want to play with us, me and my wife, as well with some cousins.

It's available lots of servers on the internet, both paid or free, but those server don't have the security I want to my kid.

That's because I had the idea of creating my own Minecraft Server on AWS. A server it will be used only by us.

I'm creating this repository to share how I did that and maybe can help other parents (or not) with the same necessity.

## Steps

It's very simple to create. To do this faster and reproduceble, I create a CloudFormation code for that.

You'll find a file called `minecraft-server.yaml`.

Basically, in this file I create a Security Group to allow only few ports I want to expose to the internet, like SSH and the port 25565 that is the port Minecraft Launcher connects.

```yaml
  MinecraftSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: 'Enable SSH and Minecraft server port'
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 25565
          ToPort: 25565
          CidrIp: 0.0.0.0/0
```

Another important part is creating the EC2 instance. Basically, you need to choose the Linux image you want, as well the instance type. I choose a `t4g.small` and it's running very well. I just need to understand if can be a smaller instance yet.

```yaml
      InstanceType: t4g.small
      ImageId: ami-0b41017b456027579
      KeyName: Minecraft Server
```

The instance type you can find in the EC2 page or using the AWS Calculator, so doing that it's easier to understand how much it will costs.

The image ID you'll find on the AMI Marketplace on the EC2 section on your AWS Management Console.

Finally, you just need to run the following command:
`aws cloudformation create-stack --stack-name MinecraftServerStack --template-body file://minecraft-server.yaml --region us-east-1`

## Security

Remember, DO NOT create that stack using your root user. Create an especific one to that. On doing that, remember to give only the necessary permissions you need. For instance, I used `AmazonEC2FullAccess` and `AWSCloudFormationFullAccess` and worked fine, but maybe can be more restricted yet.

This template doesn't create an advanced security for that server, so if you need to improve security it's important working on new layers of security in that template.

## Reboot

Now, the Minecraft server have a service to make easier to maintan when a reboot is needed. 
Following some useful commands.

1. Start the service:

```bash
sudo systemctl start minecraft.service
```

2. Check the service status:

```bash
sudo systemctl status minecraft.service
```

3. Service logs:

```bash
sudo journalctl -u minecraft.service
```

## Playing Minecraft

Now, you have your own Minecraft Server and you are able to play with your friends and family.
